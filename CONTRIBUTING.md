# Contributing

This repository currently targets BOT Chain Testnet (968). Mainnet (677) is an independent configuration and currently has no configured contract address. Read [README.md](README.md), [START-HERE.md](START-HERE.md) and [TEST-RESULTS.md](TEST-RESULTS.md).

## Development checks

Use Node.js 22 and the committed lockfile. Run:

```sh
npm ci
npm run check
npm run check:imports
npm run compile
npm test
npm run build
npm audit --audit-level=high
python3 scripts/check-upload.py
```

Describe which commands actually ran, their environment, results and limitations. Do not report an old evidence log as a new test. Existing contract tests number 41 at the reviewed commit; this count may change with future tests.

## Change requirements

- Keep the eight standalone pages, Play navigation and NFT assets intact unless the change explicitly requires otherwise.
- For contract changes, update both imported and standalone Remix sources and run their ABI/runtime equivalence test. A direct contract's executable code cannot be upgraded in place.
- For milestone changes, address the browser's fixed `STAGE_CONFIG`, contract defaults/live settings, static gallery, progression rules and metadata. The current browser does not dynamically adopt owner-edited score/time thresholds.
- Exercise wallet account/network races and transaction recovery. Do not acknowledge a mint before validating its on-chain proof. Never turn an uncertain pending transaction into a blind retry.
- Keep source/repository/explorer links consistent with the selected deployment. Mainnet footer links are not testnet transaction links.
- When updating release manifests or preservation hashes, document why each changed file is expected. The corrected `PRESERVATION.json` matches the reviewed source; current CI does not hash-verify it. Recalculate affected hashes whenever tracked file contents change.
- Document public configuration precedence and the exact publication workflow. A source push does not publish Pages.
- Never disclose keys, seed phrases or credential files. Automated tests use local test accounts; live Hardhat networks have no configured signers.

## Security and verification language

Browser scores are self-reported and can be fabricated. Nonces, timing and sequential mint checks do not prove gameplay. Do not advertise NFTs as proof of human play or unique identity.

Distinguish static findings, locally reproduced defects, local-EVM tests, read-only public-chain checks and real-wallet transactions. Do not claim a full security audit, complete device coverage or zero bugs from the automated checks.

The owner can change permitted settings and metadata or pause start/mint. ERC-721 transfer does not grant progression; pausing does not itself disable NFT transfers. Ownership renunciation is disabled and transfer uses two steps.

This documentation revision intentionally changes no executable source, contract, network settings or dependency versions.
