(base) strawhiskey@MacBook-Pro-5 stablecoin-lab-2026 % forge script script/Deploy.s.sol:Deploy \
  --rpc-url "$SEPOLIA_RPC_URL" \
  --broadcast \
  --verify \
  --private-key "$PRIVATE_KEY" \
  --etherscan-api-key "$ETHERSCAN_API_KEY" \
  -vv
[⠊] Compiling...
No files changed, compilation skipped
Script ran successfully.

== Logs ==
  admin            : 0x71218d54006F337c89d6D9f2E2033891900C1cb0
  MockUSDC         : 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e
  SimpleStablecoin : 0xda259b1D6ea75Aa4C9bDbf9C6055992319837F26
  Vault            : 0x56DBb65db1451BCeda716D334e0A7a3811499320

## Setting up 1 EVM.

==========================

Chain 11155111

Estimated max fee per gas: 2.095226086 gwei
Estimated base fee per gas: 1.047113043 gwei
Estimated max priority fee per gas: 0.001 gwei

Estimated total gas used for script: 2587089

Estimated amount required: 0.005420536359603654 ETH

==========================
##### sepolia✅  [Success] Hash: 0xd9d34a78f19238c84da5ae9a56955a68edf673d6f19f7d8de7b0782a45d8632fContract: MockUSDCContract Address: 0x3A2F5db5b319425bc31B7F17A89D555853Ec117eBlock: 11834909Paid: 0.000560760593323212 ETH (519487 gas * 1.079450676 gwei)                                                                                                    
##### sepolia✅  [Success] Hash: 0xf5e1a6a716d9306d592300ee026a691331e8c7ba8a10309cb4ef9a78c6da2207Contract: SimpleStablecoinContract Address: 0xda259b1D6ea75Aa4C9bDbf9C6055992319837F26Block: 11834909Paid: 0.001036740051102708 ETH (960433 gas * 1.079450676 gwei)                                                                                                    
##### sepolia✅  [Success] Hash: 0x75ee8c5629454cf61c0f6a2513c437269b549918865d73f336392ff428f6e924Contract: SimpleStablecoinFunction: grantRole(bytes32,address)Block: 11834909Paid: 0.000055581994757916 ETH (51491 gas * 1.079450676 gwei)                                                                                                    
##### sepolia✅  [Success] Hash: 0x87c825d4d7f8da876d2f3b21b3422b6431b0e0d1e244842aeb6b1c488868a354Contract: VaultContract Address: 0x56DBb65db1451BCeda716D334e0A7a3811499320Block: 11834909Paid: 0.000488152423052748 ETH (452223 gas * 1.079450676 gwei)                                                                                                    
✅ Sequence #1 on sepolia | Total Paid: 0.002141235062236584 ETH (1983634 gas * avg 1.079450676 gwei)                                                                                                    

==========================

ONCHAIN EXECUTION COMPLETE & SUCCESSFUL.
##
Start verification for (3) contracts
Start verifying contract `0x3A2F5db5b319425bc31B7F17A89D555853Ec117e` deployed on sepolia
EVM version: cancun
Compiler version: 0.8.24
Optimizations:    200

Verifying on etherscan...
Submitting verification for [src/MockUSDC.sol:MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Warning: Could not detect deployment: Unable to locate ContractCode at 0x3a2f5db5b319425bc31b7f17a89d555853ec117e; waiting 5 seconds before trying again (4 tries remaining)
Submitting verification for [src/MockUSDC.sol:MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Warning: Could not detect deployment: Unable to locate ContractCode at 0x3a2f5db5b319425bc31b7f17a89d555853ec117e; waiting 5 seconds before trying again (3 tries remaining)
Submitting verification for [src/MockUSDC.sol:MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Warning: Could not detect deployment: Unable to locate ContractCode at 0x3a2f5db5b319425bc31b7f17a89d555853ec117e; waiting 5 seconds before trying again (2 tries remaining)
Submitting verification for [src/MockUSDC.sol:MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Warning: Could not detect deployment: Unable to locate ContractCode at 0x3a2f5db5b319425bc31b7f17a89d555853ec117e; waiting 5 seconds before trying again (1 tries remaining)
Submitting verification for [src/MockUSDC.sol:MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Warning: Could not detect deployment: Unable to locate ContractCode at 0x3a2f5db5b319425bc31b7f17a89d555853ec117e; waiting 5 seconds before trying again (0 tries remaining)
Submitting verification for [src/MockUSDC.sol:MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Submitted contract for verification:
        Response: `OK`
        GUID: `qvnhrjj6621jigin73zhp44t7xejtiypjuh4rh3ae3g2yfgn1x`
        URL: https://sepolia.etherscan.io/address/0x3a2f5db5b319425bc31b7f17a89d555853ec117e

Verifying on sourcify...
Submitting verification for [MockUSDC] 0x3A2F5db5b319425bc31B7F17A89D555853Ec117e.
Submitted contract for verification:
        Verification Job ID: `ab74f8a3-e00f-4751-85c5-cc91b5adde45`
        URL: https://sourcify.dev/server/verify-ui/jobs/ab74f8a3-e00f-4751-85c5-cc91b5adde45

Waiting for etherscan verification result...
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Warning: Verification is still pending...; waiting 5 seconds before trying again (4 tries remaining)
Contract verification status:
Response: `NOTOK`
Details: `Already Verified`
Contract source code already verified
Start verifying contract `0xda259b1D6ea75Aa4C9bDbf9C6055992319837F26` deployed on sepolia
EVM version: cancun
Compiler version: 0.8.24
Optimizations:    200
Constructor args: 00000000000000000000000071218d54006f337c89d6d9f2e2033891900c1cb0

Verifying on etherscan...
Submitting verification for [src/SimpleStablecoin.sol:SimpleStablecoin] 0xda259b1D6ea75Aa4C9bDbf9C6055992319837F26.
Submitted contract for verification:
        Response: `OK`
        GUID: `aejhjmtwat5zemwdznqexq1qumn3msrhiwjt5tbwcwy9wequxu`
        URL: https://sepolia.etherscan.io/address/0xda259b1d6ea75aa4c9bdbf9c6055992319837f26

Verifying on sourcify...
Submitting verification for [SimpleStablecoin] 0xda259b1D6ea75Aa4C9bDbf9C6055992319837F26.
Submitted contract for verification:
        Verification Job ID: `4f1a2ba2-a316-48f8-97c4-d4d66f9807f6`
        URL: https://sourcify.dev/server/verify-ui/jobs/4f1a2ba2-a316-48f8-97c4-d4d66f9807f6

Waiting for etherscan verification result...
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Warning: Verification is still pending...; waiting 5 seconds before trying again (4 tries remaining)
Contract verification status:
Response: `NOTOK`
Details: `Already Verified`
Contract source code already verified
Start verifying contract `0x56DBb65db1451BCeda716D334e0A7a3811499320` deployed on sepolia
EVM version: cancun
Compiler version: 0.8.24
Optimizations:    200
Constructor args: 0000000000000000000000003a2f5db5b319425bc31b7f17a89d555853ec117e000000000000000000000000da259b1d6ea75aa4c9bdbf9c6055992319837f26

Verifying on etherscan...
Submitting verification for [src/Vault.sol:Vault] 0x56DBb65db1451BCeda716D334e0A7a3811499320.
Submitted contract for verification:
        Response: `OK`
        GUID: `ftxm1aduppyq8i3mg94bzydud1srvewjvscxjxwzgadmpcne7h`
        URL: https://sepolia.etherscan.io/address/0x56dbb65db1451bceda716d334e0a7a3811499320

Verifying on sourcify...
Submitting verification for [Vault] 0x56DBb65db1451BCeda716D334e0A7a3811499320.
Submitted contract for verification:
        Verification Job ID: `f799c78f-492f-4913-88a1-da29e9e847d5`
        URL: https://sourcify.dev/server/verify-ui/jobs/f799c78f-492f-4913-88a1-da29e9e847d5

Waiting for etherscan verification result...
Contract verification status:
Response: `NOTOK`
Details: `Pending in queue`
Warning: Verification is still pending...; waiting 5 seconds before trying again (4 tries remaining)
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
All (3) contracts were verified!

Transactions saved to: /Users/strawhiskey/Desktop/HKU/COMP7810/Lab1/stablecoin-lab-2026/broadcast/Deploy.s.sol/11155111/run-latest.json

Sensitive values saved to: /Users/strawhiskey/Desktop/HKU/COMP7810/Lab1/stablecoin-lab-2026/cache/Deploy.s.sol/11155111/run-latest.json

(base) strawhiskey@MacBook-Pro-5 stablecoin-lab-2026 % 