# Base Quest Milestones — BOT Chain Testnet

A browser runner with 12 sequential ERC-721 achievement badges on BOT Chain Testnet (Bohr). This is a direct, non-upgradeable contract deployment; it is not Coinbase Base and has no proxy or multisig implementation.

## Current configuration

Verified against source commit `26b7db8c68ec6fb18ad9536fdc4ac0ef7289a4bf` on 2026-10-20.

| Setting | Current value |
| --- | --- |
| Website | https://jefmark.github.io/base-quest-milestones-bot-final-testnet/ |
| Repository | https://github.com/jefmark/base-quest-milestones-bot-final-testnet |
| Selected network | `botTestnet` |
| Chain ID | `968` (`0x3c8`) |
| RPC configured in source | `https://rpc.bohr.life` |
| Testnet explorer configured in source | `https://scan.bohr.life` |
| Testnet contract | `0x5F7e7ff4F88b9C5077E9bC082a4E026cBcE320A4` |
| Mainnet contract | Blank; this repository does not currently configure a mainnet deployment |
| Public settings | `site-settings.json` |
| Package version | `1.1.0` |

The package name still contains `bot-mainnet`; it is a legacy identifier, not the selected network. WalletConnect has a public project ID configured. That does not establish compatibility with every wallet or approval of every origin.

Read [START-HERE.md](START-HERE.md) for operation and publication, [TEST-RESULTS.md](TEST-RESULTS.md) for dated evidence and limitations.

## What the application actually does

- Without a connected wallet, the browser permits practice runs. Practice cannot mint.
- With a wallet on the configured chain, one `startRun` transaction opens a session. Each of the 12 mints is another transaction. Normal continuation uses the same nonce; an existing unexpired run can be resumed after refresh without another start transaction.
- The contract advances stages sequentially, enforces timing and cooldown, and records protocol mint history per address. Transferring an NFT does not transfer progression.
- The browser begins with three lives. Losing all lives triggers a 59-second local retry lock. A clean active session can retry without another on-chain start.
- Reaching a verified checkpoint freezes gameplay while the NFT is minted. Closing its successful-mint preview continues the next stage when its chain state is verified.
- The contract charges no protocol mint fee. Start, mint and any explicit on-chain cancellation still require network gas.
- Dashboard, gallery and XP pages display a local snapshot saved by Play; they do not independently query the blockchain.

## Trust model and open issues

Scores are supplied by the caller. The contract does **not** prove human gameplay or unique identity; the existing tests demonstrate minting with fabricated plausible scores. Browser integrity checks are bypassable and are not a trusted anti-cheat system.

The owner can pause/unpause, edit permitted milestone settings, change cooldown and metadata URI, change the compatibility treasury address and transfer ownership through `Ownable2Step`. Renunciation is disabled. Non-upgradeable bytecode does not make these settings immutable.

Known unresolved issues include fixed browser thresholds versus editable contract thresholds, expired frozen checkpoints without an in-page recovery action, and links to the previous mainnet repository/explorer. The documentation update does not fix runtime code.

## Development and verification

Use Node.js 22 (`>=22 <23`) and the committed lockfile.

```sh
npm ci
npm run check
npm run check:imports
npm run compile
npm test
npm run build
npm run preview
```

`npm run dev` starts Vite. `npm run build` runs the client/static checks, bundles the website, then finalizes metadata and static-page configuration. It does not itself run Hardhat tests or the dependency audit; the workflows run those separately.

A local build with no public URL or GitHub repository environment uses `http://localhost:4173/` in generated metadata. Do not publish that output as production. GitHub Actions derives the Pages URL, or `publicAppUrl` may explicitly supply it.

## Publication and contract source

GitHub Pages publication is manual through **Deploy Base Quest to GitHub Pages**. Pushes run CI and Security; they do not publish. The deployment workflow accepts the default branch, checks the upload manifest/configuration/dependencies, compiles and tests, then publishes `dist`.

`contracts/BaseQuestMilestones.sol` imports OpenZeppelin 5.0.2. `remix/BaseQuestMilestones.sol` is the bundled standalone equivalent. Compiler settings: Solidity 0.8.24, optimizer enabled with 200 runs, EVM `paris`, viaIR disabled. Constructor arguments: `initialOwner`, `initialBaseURI`, `initialTreasury`; deployment value is zero. Existing testnet use does not require another deployment.

License: [MIT](LICENSE).
