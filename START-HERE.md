# Start here — operate the existing BOT Testnet project

This guide describes the source reviewed on 2026-10-20. The current `site-settings.json` already selects `botTestnet` and includes contract `0x5F7e7ff4F88b9C5077E9bC082a4E026cBcE320A4`. Do not treat this release as an unconfigured mainnet/practice-only package.

## 1. Use the existing website

Open https://jefmark.github.io/base-quest-milestones-bot-final-testnet/.

Without a connected wallet, use Start Practice Run. To use the NFT flow, connect a compatible EVM wallet on BOT Chain Testnet, chain 968, and have test BOT for gas. The configured RPC is `https://rpc.bohr.life`; the corresponding explorer is `https://scan.bohr.life`.

A newly authorized run needs one start transaction; resuming an active run does not. Each milestone mint is a separate transaction with zero protocol payment. Check that the destination is the configured contract and the chain is 968. The game does not require token spending approvals or private keys.

Keep the game visible while playing: hiding the tab or a long frame gap invalidates the local run. Wallet interaction at a frozen mint checkpoint is handled separately. After three lost lives, the local retry delay is 59 seconds. After successful minting, close the NFT preview to continue.

Refresh restores an authorized wallet through `eth_accounts` when possible. If the restored wallet is on another chain, the code may request network addition/switching. Locked/revoked wallets and expired WalletConnect sessions may require reconnection. Refresh loses unminted local score/position; on-chain progression remains.

## 2. Recover carefully after an interrupted transaction

Use **Check pending transaction** when shown. A missing receipt is not proof that a transaction failed. The app can reconcile mined transactions and some sender/nonce replacements, then **Sync confirmed NFT** without sending a duplicate mint.

Recovery requires responsive RPC state. Replacement lookup can query historical account nonces. An old hash-only record whose original transaction is unavailable may remain unresolved. Do not clear browser storage or blindly resubmit while a transaction is uncertain.

If an unsubmitted frozen checkpoint has exceeded the on-chain 24-hour stage lifetime, the current UI cannot discard it directly. First verify that no mint is pending/confirmed; a reload discards that local checkpoint, after which an expired chain run can be replaced with a new start. This loses the unminted local attempt. There is no in-page Cancel Run control despite an exported wallet cancellation function.

Owner changes to score/time requirements can also make the fixed browser checkpoint unusable. Waiting does not increase its frozen play time or score. Keep production milestone values synchronized with the browser until this is fixed; a reload alone does not solve a persistent threshold mismatch.

## 3. Change public website settings

Edit `site-settings.json`:

| Key | Meaning |
| --- | --- |
| `network` | `botTestnet` or `botMainnet`; currently testnet |
| `testnetContractAddress` | Direct testnet contract; currently populated |
| `mainnetContractAddress` | Separate mainnet configuration; currently empty |
| `walletConnectProjectId` | Public Reown/WalletConnect project identifier; currently populated |
| `publicAppUrl` | Empty means derive the Pages URL in Actions; specify an HTTPS base URL for an explicit deployment URL |

A blank selected contract address enables practice-only operation. Addresses must be nonzero EVM addresses. Keep JSON valid. The current build loader permits explicit process environment and Vite environment-file values to override JSON settings; `site-settings.json` is not an unconditional override. The Pages workflow does not explicitly import old repository `VITE_*` variables.

Do not store wallet secrets in this file. WalletConnect requires the target chain to be approved in the wallet session and appropriate project/origin configuration; a populated project ID alone does not guarantee pairing.

## 4. Publish website changes

1. Commit the complete source to the existing repository. If uploading manually, preserve directory structure, including `.github/workflows` and `public/nft`.
2. Set Settings → Pages → Source to GitHub Actions.
3. Run **Deploy Base Quest to GitHub Pages** from Actions on the default branch.
4. Wait for both build and deploy jobs to succeed, then refresh the website.
5. Check the game, `metadata/1.json`, `metadata/12.json` and their image URLs.

Push/PR events run CI and Security but do not deploy. `UPLOAD-FILES.json` is checked for missing/empty required files. That check does not verify file hashes. `PRESERVATION.json` records SHA-256 hashes for selected files. Its corrected replacement matches the reviewed source at commit `26b7db8c68ec6fb18ad9536fdc4ac0ef7289a4bf`; it is not a guarantee that every repository file is covered. Current CI does not verify these hashes.

Documentation-only changes on GitHub do not change the deployed website text. The existing in-app GitHub link still points to the old mainnet repository and the generic explorer footer still points to mainnet; those require separate code changes.

## 5. Deploy another contract only when needed

The configured testnet contract already exists. Source changes to a direct non-upgradeable contract require another deployment to change executable behavior; a website rebuild cannot upgrade that contract.

For an intentional new deployment, upload `remix/BaseQuestMilestones.sol` to Remix and select `BaseQuestMilestones`:

- Solidity 0.8.24; optimizer enabled, 200 runs; EVM `paris`; viaIR disabled.
- Use the wallet browser-extension provider and verify the selected account/network.
- Constructor `initialOwner`: intended nonzero admin wallet.
- Constructor `initialBaseURI`: complete published metadata directory, e.g. `https://jefmark.github.io/base-quest-milestones-bot-final-testnet/metadata/`.
- Constructor `initialTreasury`: nonzero compatibility treasury address; this version has no mint fee collection.
- Value: zero. Approve deployment only in your wallet and retain the transaction hash, source and compiler settings.

The constructor normalizes a missing trailing slash; it does not validate that the URI is a usable public metadata endpoint. Verify it yourself.

Check `deploymentKind` = `BASEQUEST_FIXED_V1`, `MAX_MILESTONE` = 12, the owner, `paused`, cooldown, each milestone and metadata. Then update the correct address in JSON and publish the frontend. Token IDs are global mint serial numbers; `tokenURI(tokenId)` uses that token's milestone, so token ID 12 need not be milestone 12.

Mainnet is a distinct deployment/configuration: BOT Chain 677, source RPC `https://rpc.botchain.ai`, explorer `https://scan.botchain.ai`. This repository's current mainnet address is empty. Do not reuse testnet NFTs/progress or claim that testnet acceptance proves every mainnet wallet flow.

## 6. Checks still needed with real wallets

Test connect/reject/retry, refresh, account/network switching, start, mint, preview dismissal, timeout recovery, speed-up/cancellation and mobile return-to-game. Confirm transaction destination, chain, zero protocol value and final metadata. The local test suite uses mocks and a local EVM; the 2026-10-20 review sent no public-chain transaction.

See [TEST-RESULTS.md](TEST-RESULTS.md) for exact completed checks.
