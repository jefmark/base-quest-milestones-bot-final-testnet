# Verification record — 2026-10-20

Reviewed source: `26b7db8c68ec6fb18ad9536fdc4ac0ef7289a4bf` in `jefmark/base-quest-milestones-bot-final-testnet`.

This record replaces stale mainnet release claims with checks performed for the current testnet tree. Earlier reports remain available in Git history at the reviewed commit; they are not additional checks performed today. This is a scoped source review and test execution, not a guarantee of zero bugs or an independent formal security audit.

## Executed checks

| Check | Result and scope |
| --- | --- |
| Dependency installation | `npm ci --ignore-scripts --no-audit --no-fund` completed; lifecycle scripts were deliberately not run |
| Runtime | Initial checks used Node 24.19.0 and produced an engine mismatch warning; build and all contract tests were then rerun successfully under Node 22.23.3 |
| `npm run build` | Passed under Node 22.23.3, including all checks in `npm run check`, Vite and both finalizers |
| `npm test` | 41 passing under Node 22.23.3; Hardhat compilation/test path completed |
| `npm run check:imports` | All 12 declared imports resolved |
| `python3 scripts/check-upload.py` | Passed; required listed files exist and are nonempty |
| Full `npm audit` | 13 advisory entries: 9 low, 4 moderate, 0 high, 0 critical; development dependency paths |
| Production dependency audit | `npm audit --omit=dev`: 0 reported advisory entries |
| Preservation hash comparison | FAILED for `src/main.js` and `src/wallet.js`; all other listed entries matched |
| Checkpoint edge-case harness | Reproduced a frozen 21-second payload still locally mintable after chain elapsed time 86,422 seconds; uses synthetic score/time setup with unchanged production checkpoint functions |
| Public site browser check | Site rendered; Start Practice Run changed to Run Active with mint disabled; this was a desktop cloud-browser practice check |

Vite reports its large-chunk warning and a removable third-party PURE annotation warning. These are warnings, not build failures. The normal local build finalized metadata to `http://localhost:4173/`; that output was not deployed. The standard build succeeding does not establish that preservation hashes match, because no current check validates those hashes.

The browser's captured errors were extension-origin metadata messages, not confirmed application errors. No real wallet was connected during browser inspection. A local Playwright attempt could not launch because its Chromium binary was unavailable; it is not counted as a passed desktop/mobile automation suite.

## Public testnet reads

At approximately **2026-10-20 11:41:36 UTC** (14:41:36 Europe/Istanbul), read-only calls to the configured RPC returned:

| Field | Observed value |
| --- | --- |
| Chain | `0x3c8` = 968 |
| Contract | `0x5F7e7ff4F88b9C5077E9bC082a4E026cBcE320A4` |
| `deploymentKind()` | `BASEQUEST_FIXED_V1` |
| Runtime bytecode size | 13,477 bytes |
| Runtime comparison | Equal to locally compiled imported contract after removing trailing compiler CBOR metadata |
| `owner()` | `0xe5D5718Eb7b3920d2BcCB1fB674500b203F8D34c` |
| `paused()` | `false` |
| `totalMinted()` | 22 |
| `mintCooldown()` | 40 seconds |
| `feeEnabled()` | `false` |
| Milestones 1–12 | Active; score/time/name values match current constructor and `STAGE_CONFIG` defaults |
| `tokenURI(1)` | `https://jefmark.github.io/base-quest-milestones-bot-final-testnet/metadata/1.json` |

These are dated RPC observations, not immutable facts. Calls used `latest`, not a single pinned block, and were not independently corroborated against a second RPC. Runtime equality excludes source metadata; it is not a claim that explorer source verification was performed.

All 12 published metadata JSON files and each referenced image URL were fetched successfully (HTTP 200).

## What the automated tests cover

The current tests include standalone/imported contract equivalence; ABI compatibility; all 12 sequential mints through the shipped wallet path on a local EVM; ownership/pause/transfer rules; reentrancy and receiver-rejection rollback; nonce/expiry behavior; wallet transaction locking; account-bound reads; timeout and replacement recovery; restoration and client gameplay checks.

The suite also intentionally demonstrates that plausible fabricated browser scores can mint. No human gameplay is needed for that test. Passing it documents the trust limitation rather than solving it.

## Open findings and untested scope

The source still has fixed browser thresholds versus editable contract requirements, expired frozen checkpoints without an in-page discard action, and generic links to the previous mainnet repository/explorer. In particular, the live deployment currently uses default milestone values, so the owner-change incompatibility is a confirmed conditional defect, not a claim that today's live thresholds already differ.

No public-network transaction, deployment, mint, cancellation or administrative call was sent. Actual MetaMask/mobile/WalletConnect pairing, hardware wallets, complete responsive/accessibility coverage, RPC outage/reorg resilience and GitHub's remote workflow execution were not fully validated. Local test success is not evidence that every live integration works.

The four existing Markdown files were rewritten in English. Runtime code, contract sources, public assets, settings and lockfile were left unchanged. Historical `evidence/` logs were not overwritten or relabeled as current evidence.

## Preservation manifest correction

After the review, all 55 listed SHA-256 hashes were recalculated against the local source at the reviewed commit. Only the entries for `src/main.js` and `src/wallet.js` required corrections. The corrected replacement preserves the entry list and historical fields. This corrects the manifest only; it changes no executable file and adds no CI verification. The earlier failed comparison above remains a record of the original manifest.
