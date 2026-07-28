## Description

This PR adds a **complete testnet integration testing infrastructure** for the Stellar Portfolio Rebalancer smart contract, including a **mock reflector oracle contract**, a **real on-chain test suite**, and a **nightly CI workflow**. Fixes #964.

### What's Included

#### 1. Mock Reflector Oracle (`contracts/mock-reflector/`)
A minimal Soroban contract returning fixed $100.00 prices with the current ledger timestamp for deterministic testnet tests.

#### 2. Testnet Integration Tests (`contracts/tests/testnet_integration.rs`)
| # | Test |
|---|------|
| 1 | Full deploy → initialize → create → deposit → rebalance lifecycle |
| 2 | Three-way 33.33/33.33/33.34% fractional allocations with drift preview |
| 3 | Emergency stop blocks rebalance → re-enable → rebalance succeeds |
| 4 | Config view, capability summary, version endpoints |
| 5 | Fee config flow (default → set → verify) |

Includes CLI helpers, tx hash logging, event verification, and `TestnetFixture` pattern.

#### 3. CI Workflow (`.github/workflows/integration-tests.yml`)
- **integration-tests** (every PR): mock-based only, skips testnet
- **testnet-integration** (nightly 4 AM UTC + manual dispatch): deploys both contracts and runs on-chain tests

#### 4. Makefile Targets (`contracts/Makefile`)
`build-mock-reflector`, `build-testnet`, `testnet-integration`, `testnet-only`, `test-integration`

### CI Infrastructure Fixes
Also included are fixes for pre-existing CI failures affecting all workflows:
- `frontend/.npmrc`, `backend/.npmrc`: `legacy-peer-deps=true` for npm ci compatibility
- `commitlint.config.cjs`: Ignore merge commits and conflict resolution commits
- `backend/.env.example`: Removed duplicate keys
- `backend/src/{api,queue,test}/`: Fixed orphaned TypeScript code (TS1128 errors)
- `frontend/package.json`: Fixed Storybook 8.x compatibility

## Type of Change
- [x] New feature
- [x] DevOps / CI / Documentation update

## Checklist
- [x] Code follows project style guidelines
- [x] Tests added (5 testnet integration tests)
- [x] Fixes #964
