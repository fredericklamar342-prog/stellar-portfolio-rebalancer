## Description

This PR adds automated **property-based testing** for the Stellar Portfolio Rebalancer Soroban contract using the `proptest` crate. Fixes #963.

### What's Included

#### Property Tests (`contracts/tests/property_tests.rs`)

| ID   | Property | Invariant |
|------|----------|-----------|
| P1a  | Valid allocations accepted | Non-empty `u32` vector summing to 10000 with no zeroes → accepted |
| P1b  | Invalid allocations rejected | Vectors with sum ≠ 10000, zeroes, or empty → rejected |
| P2a  | Drift range | `compute_drift(c, t)` ∈ `[0, 10000]` for all valid inputs |
| P2b  | Drift symmetry | `drift(a, b) == drift(b, a)` |
| P2c  | Zero drift at equality | `drift(p, p) == 0` |
| P3a  | Rebalance idempotency | `trade_amount == 0` when `current_balance == target_balance` |
| P3b  | Trade sign correctness | Over-weight → sell (≤0); Under-weight → buy (≥0) |

Each property is verified across **10,000 randomly-generated inputs** using the production `portfolio::value_to_balance` function so arithmetic scaling regressions are caught.

#### CI Workflow (`.github/workflows/property-tests.yml`)
- Runs on every PR/push touching `contracts/**`
- Uses Rust stable with wasm32 target
- Uploads test results and failure corpus as artifacts
- Bash strict mode (`set -euo pipefail`) for reliable exit codes

#### Contract ABI Updates (`contracts/CONTRACT_ABI.md`)
- Documented all 7 property invariants (P1-P3b)
- Added basis-point (10000) conventions

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
- [x] Tests added (7 property tests, 10,000 cases each)
- [x] Fixes #963
