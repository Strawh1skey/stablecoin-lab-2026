# STUDENT-QUESTIONS.md — Discussion questions (submit with your repo)

Answer directly under each question. 150–300 words each — **reasoning over length**.

---

## A. Permission design

**A1.** The vault holds `MINTER_ROLE`, so it can `burn` any user's balance. Explain why that is a risk, then write out how you would change `Vault` and `SimpleStablecoin` to remove it.

> Your answer:

The risk is not that `onlyRole(MINTER_ROLE)` is missing. The access check works, but the role is too powerful. In the current design the same role authorizes both `mint(to, amount)` and `burn(from, amount)`, and the `Vault` holds that role so it can service deposits and redemptions. As a result, a compromised or buggy vault can destroy sUSD from any address, even when that user did not ask to redeem. This creates a centralization and key-compromise risk that is separate from normal ERC-20 ownership.

I would keep `MINTER_ROLE` only for creating supply and remove the arbitrary `burn(address from, uint256 amount)` interface. `SimpleStablecoin` should instead expose a holder-controlled burn, for example `burn(uint256 amount)` that can only burn `msg.sender`, or a `burnFrom` mechanism that requires an allowance from the holder. Then I would change `Vault.redeem` so the user explicitly authorizes the vault to spend sUSD. The vault can pull the approved sUSD, burn the amount it actually received, and only then release the matching collateral. This means possession of the mint role no longer gives the vault an independent ability to erase arbitrary user balances. It also makes redemption authority explicit and auditable: a user's tokens are burned only as part of a redemption the user authorized.


**A2.** In this contract `DEFAULT_ADMIN_ROLE`, `MINTER_ROLE` and `PAUSER_ROLE` all go to the same address. How would you split them in production, and who holds each?

> Your answer:

I would not let one externally owned account hold `DEFAULT_ADMIN_ROLE`, `MINTER_ROLE`, and `PAUSER_ROLE` at the same time. These permissions have different purposes and different failure consequences, so production design should follow least privilege.

`DEFAULT_ADMIN_ROLE` should be held by a governance multisig, preferably behind a timelock for non-emergency changes. Its job is to grant and revoke operational roles, not to mint tokens during normal operation. `MINTER_ROLE` should be granted only to the vault or other narrowly scoped issuance contracts that can mint only as part of a collateralized workflow. I would avoid giving this role directly to a human wallet. `PAUSER_ROLE` should be held by a separate security or emergency multisig that can react quickly to an incident but cannot change administrators or create supply.

This split limits the blast radius of one compromised key. A compromised pauser may interrupt service but cannot mint. A compromised operational vault should not automatically gain governance authority. A compromised administrator is still serious, but a multisig and timelock make that attack harder and more visible. I would also monitor role changes on-chain and keep the number of role holders small, because permission changes are part of the stablecoin's security boundary, not just administrative housekeeping.


---

## B. Pausing and redemption

**B1.** `_update` is the single entry point for every balance change, so `pause()` freezes transfers, minting and redemption together. If you wanted "pause transfers but **allow redemption**", how would you change it? Give the approach — full code not required.

> Your answer:

I would remove the blanket `whenNotPaused` restriction from the single `_update` path and make the pause policy depend on the type of balance change. The current design treats transfers, minting, and burning identically because all three eventually call `_update`. That is convenient, but it means an emergency transfer pause also disables the burn used by redemption.

A better design would allow the contract to distinguish an ordinary transfer from minting or burning. In ERC-20 terms, an ordinary transfer has both `from` and `to` nonzero, minting has `from == address(0)`, and burning has `to == address(0)`. While paused, I would block ordinary transfers and normally block new minting, but permit the controlled burn path needed for redemption. If I also apply the A1 redesign, the burn should be tied to a user-authorized redemption rather than an unrestricted privileged burn.

I would also separate the concepts of `transfersPaused` and `redemptionsPaused` instead of using one global switch. Redemption should remain available during most incidents because it is the mechanism that connects sUSD to its backing asset. A separate redemption shutdown could still exist for an extreme case such as proven insolvency or a broken custody rail, but it should be a distinct, more tightly governed emergency action.


**B2.** In 2008, when a money-market fund "broke the buck", redemptions were frozen for days. In 2023 USDC depegged to $0.87 after a reserve bank failed, but redemptions were **not** shut. Compare the two responses — what does closing the redemption channel, or leaving it open, do to a stablecoin?

> Your answer:

Closing redemption and leaving redemption open have very different effects on a stablecoin. If redemption is open and the issuer can still honor it, a market price below $1 creates an arbitrage opportunity: a trader can buy the token at a discount and redeem it for close to $1 of collateral. That mechanism creates demand for the discounted token and is one of the main forces pulling the market price back toward the peg. This is why the redemption channel is more than a convenience; it is part of the price-stabilization mechanism.

If redemption is closed, the balance-sheet invariant can still look healthy. The vault may still have enough collateral for every token. However, holders can no longer convert the token into the asset that is supposed to give it value. The problem has changed from a pure solvency question into a liquidity and confidence problem, and the market may rationally trade the token below $1 because immediate par redemption is unavailable.

Leaving redemption open is not risk-free. If reserves are impaired or illiquid, rapid redemptions can drain the most liquid assets and create a first-mover problem. For that reason, a real system needs reserve liquidity, transparent reporting, and carefully designed emergency controls. I would prefer pausing transfers separately while preserving redemption whenever the reserve and settlement rails can still support it.


---

## C. Depeg analysis

**C1.** Under what conditions does this coin depeg? Distinguish at least two classes of cause, and say how each one shows up in the invariant `totalCollateral() >= totalSupply()`.

> Your answer:

I would separate depeg risk into at least solvency failure and liquidity failure. A solvency failure means the system has issued more redeemable claims than the collateral can support. In this lab, the clearest example is Ex3: an attacker obtains `MINTER_ROLE` and creates sUSD without depositing mUSDC. `totalSupply()` increases while `totalCollateral()` does not, so eventually `totalCollateral() >= totalSupply()` becomes false. Loss or theft of reserve assets would create the same type of failure from the opposite direction by reducing collateral instead of increasing supply.

A liquidity failure is different. The system may still satisfy `totalCollateral() >= totalSupply()`, but holders cannot actually exercise the $1 redemption promise. The lab's pause design demonstrates this because pausing `SimpleStablecoin` blocks the burn inside `Vault.redeem`. The collateral can still be present and the numerical invariant can still hold, yet sUSD can trade below $1 because the path from the token to its collateral is closed.

This is why I view the invariant as necessary but not sufficient. It measures backing, not accessibility. A credible stablecoin needs both adequate collateral and a functioning redemption mechanism. Operational failures, custody restrictions, or settlement outages can therefore produce a depeg even when the on-chain backing arithmetic still looks correct.


**C2.** Suppose an attacker bribes their way to `MINTER_ROLE`, mints 1,000,000 sUSD out of nothing and redeems it all. Describe the flow of funds, and name the step that could have stopped them.

> Your answer:

Assume the vault already contains collateral deposited by honest users. After obtaining `MINTER_ROLE`, the attacker calls `mint(attacker, 1_000_000e6)` directly. No mUSDC enters the vault, so this step creates one million new redemption claims without creating one million units of new backing. At that point the supply-to-collateral relationship has already deteriorated.

The attacker can then call `Vault.redeem` with the unbacked sUSD. The vault burns the attacker's sUSD and transfers the same amount of mUSDC out of its pooled collateral, subject to the vault actually having enough collateral to satisfy the requested redemption. Economically, the attacker converts a permission-created token into reserve assets that originally backed honest users. After enough redemptions, the vault is depleted while honest sUSD may remain outstanding. The `InsufficientCollateral` check only stops redemptions once the reserve is already insufficient; it does not distinguish legitimately minted sUSD from maliciously minted sUSD.

The best place to stop this attack is before the unbacked mint. `MINTER_ROLE` must be tightly controlled, and production minting should be reachable only through a contract path that proves matching collateral was received. Stronger admin separation, a multisig or timelock for role changes, mint caps, and monitoring of role grants would reduce the chance that one compromised permission can create arbitrary redeemable liabilities.


---

## D. Toward RWA

**D1.** Right now the collateral is `MockUSDC` and `totalCollateral()` just reads an on-chain balance — simple and reliable. If the collateral were **US Treasuries**, could this invariant still be written that way? What new problems appear?

> Your answer:

If the reserve were US Treasuries, `totalCollateral()` could no longer be defined as a simple ERC-20 `balanceOf` call unless the on-chain token represented a legally enforceable and accurately reconciled claim on the underlying securities. The real assets would normally sit with a custodian or broker, while the blockchain would only contain a record or tokenized representation of them.

The invariant would therefore need a value layer, not just a token-count layer. A more realistic form would be something like `verifiedReserveValueAfterHaircuts >= stableSupplyValue`. The reserve value would need to account for Treasury prices, accrued interest, maturity, settlement timing, and any cash balance. I would also apply liquidity or valuation haircuts because a security worth $1 million on paper is not identical to $1 million of immediately available cash.

This introduces new trust and freshness problems. The system needs evidence that the custodian actually holds the securities, that they are not pledged elsewhere, and that the on-chain report is current. It also needs an oracle or attestation process to convert the portfolio into a consistent dollar value. Legal ownership becomes part of the invariant too: even if the securities exist, token holders need an enforceable claim on them. The on-chain contract can verify data that is supplied to it, but it cannot independently verify an off-chain brokerage account.


**D2.** If the collateral were **a building**, how would you put it inside this vault? Which off-chain roles or legal structures would you have to introduce?

> Your answer:

I would not try to make the smart contract directly "own" a building. A physical property needs a legal wrapper that can hold title and connect on-chain tokens to enforceable off-chain rights. A common structure would be a special-purpose vehicle (SPV) that legally owns the building. The vault's collateral would then represent a claim on shares, debt, or another legally defined interest in that SPV rather than the building itself.

Several off-chain roles become necessary. A trustee, corporate administrator, or regulated custodian would maintain the legal link between token holders and the SPV. The land registry and legal counsel establish title and identify liens. An independent appraiser or valuation provider supplies updated property values. A property manager handles rent, maintenance, taxes, and insurance, while an auditor or attestation provider verifies the asset and cash flows. An oracle or reporting service is then needed to bring selected facts on-chain.

The biggest difference from mUSDC is liquidity. A building cannot be sold instantly to satisfy redemptions, and its appraised value can change slowly or be uncertain. I would therefore use conservative valuation haircuts and probably maintain a separate liquid reserve for normal redemptions. The relevant invariant would be based on verified net asset value after debt, liens, costs, and haircuts, not simply the number of property tokens recorded by the contract.


---

## E. Tests (Tier 1 required — this is Ex4)

Turn the red tests green in `test/exercises/01_LoopTasks.t.sol` to cover the scenarios below, and write your test function names here:

| Scenario | Your test function name |
|---|---|
| Minting by a non-minter reverts | `test_Ex4_Mint_RevertsForNonMinter` |
| Transfers revert while paused | `test_Ex4_Pause_BlocksTransfers` |
| **Redemption** reverts while paused | `test_Ex4_Pause_BlocksRedeem` |
| An attacker cannot burn someone else's balance | `test_Ex4_AttackerCannotBurnOthersBalance` |
| ...but the vault holding `MINTER_ROLE` can | `test_Ex4_VaultHoldsTheKey_CanBurnAnyonesBalance` |

That last pair is meant to be read together: the guard is written correctly, but the key was handed to the vault. Keep it in mind when you answer A1.

Now write one more scenario you consider **most likely to be attacked**, and say why you picked it:

> Your answer:

The additional scenario I would test is compromise of `DEFAULT_ADMIN_ROLE`. The existing non-minter test proves that an outsider cannot mint directly, but Ex3 shows that this protection is only as strong as the account that can grant `MINTER_ROLE`. I would simulate an administrator granting `MINTER_ROLE` to an attacker, let the attacker mint without depositing collateral, and then assert that the backing invariant can be broken. I consider this a realistic high-impact target because an attacker does not need to defeat the ERC-20 arithmetic or the `onlyRole` check. They only need to compromise the authority that manages roles. A production design should therefore treat admin-key security, multisig policy, timelocks, role-change monitoring, and emergency revocation as part of the stablecoin's core security model.
