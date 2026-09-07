# Project: openOODA/blackbox Blue Ocean Features Upgrade

## Architecture
openOODA/blackbox is a zero-dependency, capability-secure introspection and telemetry flight recorder for openOODA.
This upgrade adds agent-native Blue Ocean capabilities across the existing 5 subsystems:
1. `autopsy/`: Autonomous Self-Healing Engine (`autopsy/self_heal.oo`) consuming crash autopsy coordinates (`file:line:col`), generating safe in-memory source patches, applying under `FsWriteCap`, validating via `oodac check`, and failing closed on invalid coordinates without file corruption.
2. `record/`: Portable Flight Capsules (`record/flight_capsule.oo`) bundling active flight telemetry (`.blackbox/flight.json`), crash autopsy state, register snapshots (`RIP`, `RSP`, `RBP`), and capability bitmasks into a single `.bb` container authenticated with a pure-.oo CRC32 checksum.
3. `bridge/`: Real-Time Crash Sentinel Daemon (`bridge/dir_watcher.oo`) monitoring target directories for `.blackbox/autopsy.json` and `.blackbox/flight.json`, detecting creation/modification, and automatically triggering autopsy diagnosis and emitting 1-turn patch recipes.
4. `cli/`: Extended CLI dispatch in `cli/cli_run.oo` (and modular helper `cli/cli_capsule.oo`) routing `heal`, `pack`, `replay`, and `watch` with strict capability bitmask enforcement.
5. `qa/`: Multi-tier verification suites (`qa/heal_proof.oo`, `qa/capsule_proof.oo`, `qa/watch_proof.oo`, `qa/e2e_blue_ocean_proof.oo`) enforcing `Run_1 == Run_2` determinism proofs.
6. `qa/`: Master test runner (`qa/suite.oo`) and surface verifier (`qa/verify_surface.oo`); CI is the entry point (no `scripts/` runners).

## Feature Inventory
| # | Feature | Description | Milestone | Source |
|---|---------|-------------|-----------|--------|
| 1 | Autopsy Coordinate & Guard Ingestion | Parse `FailureCoordinate` and extract suggested patch guard from autopsy JSON | M5 | R1 / Survey |
| 2 | In-Memory Pure Patch Generator | Generate indented patch insertion before failure line with idempotency checking | M5 | R1 / Survey |
| 3 | Safe Patch Application under FsWriteCap | Apply patch safely to target source file under capability enforcement | M5 | R1 / Survey |
| 4 | Compiler Check Validation & Rollback | Validate patched file via `oodac check`; roll back immediately in-memory if invalid | M5 | R1 / Survey |
| 5 | Fail-Closed Bounds & Traversal Guards | Reject out-of-bounds line numbers and `..` paths with zero file mutation | M5 | R1 / Survey |
| 6 | CLI `blackbox heal` Command | Dispatch `blackbox heal [autopsy.json]` fail-closed | M5 | R1 / Survey |
| 7 | Self-Healing Double-Run Determinism Proof | Prove `Run_1 == Run_2` on patch generation and healing | M5 | R1 / R4 / Survey |
| 8 | Pure-.oo CRC-32 Checksum Algorithm | Standard IEEE 802.3 CRC-32 implementation without FFI or external libraries | M6 | R2 / Survey |
| 9 | Single-File `.bb` Capsule Serialization | Bundle flight events, autopsy dump, CPU registers, and capabilities into `.bb` | M6 | R2 / Survey |
| 10 | Capsule Unpacking & Integrity Verification | Unpack `.bb`, compute checksum, and verify integrity fail-closed on tampering | M6 | R2 / Survey |
| 11 | Register Snapshot & Deterministic Reconstruction | Reconstruct CPU registers (`RIP`, `RSP`, `RBP`) and full 1-turn crash diagnosis | M6 | R2 / Survey |
| 12 | CLI `blackbox pack` Command | Dispatch `blackbox pack` producing `.bb` capsule file | M6 | R2 / Survey |
| 13 | CLI `blackbox replay` Command | Dispatch `blackbox replay <file.bb>` reconstructing crash state | M6 | R2 / Survey |
| 14 | Capsule Double-Run & Tamper Proofs | Prove `Run_1 == Run_2` serialization and verify corrupted file rejection | M6 | R2 / R4 / Survey |
| 15 | Stateful Directory Monitoring Engine | Track existence and byte lengths of `.blackbox/` crash artifacts across ticks | M7 | R3 / Survey |
| 16 | Artifact Creation & Modification Detection | Detect newly created or modified autopsy and flight files | M7 | R3 / Survey |
| 17 | Automated 1-Turn Recipe Trigger | Automatically invoke autopsy parser and emit 1-turn repair recipe to stdout | M7 | R3 / Survey |
| 18 | CLI `blackbox watch` with `--once` / `--max-ticks` | Headless, batch, and daemon monitoring options in CLI | M7 | R3 / Survey |
| 19 | Sentinel Double-Run & Headless Proofs | Prove deterministic emission and non-blocking headless execution | M7 | R3 / R4 / Survey |
| 20 | Surface Verifier CLI Update | Extend `scripts/verify_surface.oo` to verify `heal`, `pack`, `replay`, `watch` CLI tools | M8 | R4 / Survey |
| 21 | Master Test Suite Integration | Wire all proofs (`heal_proof`, `capsule_proof`, `watch_proof`, `e2e`) into `scripts/test.oo` | M8 | R4 / Survey |
| 22 | Master Verify Script Integration | Ensure `scripts/verify.oo` validates updated blackbox surface | M8 | R4 / Survey |
| 23 | Constitutional Conformance Invariants | Verify pure `.oo`, <= 256 lines/file, and 4-element Academy headers repository-wide | M8 | R4 / Survey |
| 24 | Strict Quarantine Verification | Verify `openOODA/oodac` remains 100% clean (`git status -s` is empty) | M8 | R4 / Survey |
| 25 | Full Multi-Tier E2E Proofs | Complete Tier 1-4 test executions passing with zero failures | M8 | R4 / Survey |

## Milestones
| # | Name | Scope | Dependencies | Status |
|---|------|-------|-------------|--------|
| M5 | Autonomous Self-Healing Engine (`blackbox heal`) | `autopsy/self_heal.oo`, `autopsy/ANCHOR.oo`, `cli/cli_run.oo` (heal), `qa/heal_proof.oo` | None | DONE |
| M6 | Portable Flight Capsules (`pack` & `replay`) | `record/flight_capsule.oo`, `record/ANCHOR.oo`, `cli/cli_capsule.oo`, `cli/cli_run.oo` (pack/replay), `qa/capsule_proof.oo` | M5 | DONE |
| M7 | Real-Time Crash Sentinel Daemon (`blackbox watch`) | `bridge/dir_watcher.oo`, `bridge/ANCHOR.oo`, `cli/cli_watch.oo`, `cli/cli_run.oo` (watch), `qa/watch_proof.oo` | M5, M6 | DONE |
| M8 | E2E Integration, Dual-Run Proofs & Victory Audit | `scripts/verify_surface.oo`, `scripts/test.oo`, `scripts/verify.oo`, `qa/e2e_blue_ocean_proof.oo`, full suite run, line count & header audit, quarantine check | M5, M6, M7 | DONE |

## Interface Contracts
### `autopsy/self_heal.oo`
- `heal_extract_guard(remediation: String, root_cause: String) -> String`
- `heal_generate_patch(source_text: String, coord: FailureCoordinate, guard: String) -> Result[String, String]`
- `heal_apply(fw: &FsWriteCap, file_path: String, patched_text: String) -> Result[Void, String]`
- `heal_validate_and_rollback(p: &ProcessCap, fs: &FsReadCap, fw: &FsWriteCap, env: &EnvCap, file_path: String, orig_text: String) -> Result[Void, String]`
- `heal_from_autopsy_data(p: &ProcessCap, fs: &FsReadCap, fw: &FsWriteCap, env: &EnvCap, data: AutopsyData) -> Result[String, String]`
- `heal_from_autopsy_json(p: &ProcessCap, fs: &FsReadCap, fw: &FsWriteCap, env: &EnvCap, json_text: String) -> Result[String, String]`

### `record/flight_capsule.oo`
- `capsule_crc32(payload: String) -> Int`
- `capsule_pack_data(flight_json: String, autopsy_json: String, rip: String, rsp: String, rbp: String, caps_mask: Int) -> String`
- `capsule_pack(fs: &FsReadCap, fw: &FsWriteCap, out_path: String) -> Result[String, String]`
- `capsule_unpack(fs: &FsReadCap, capsule_path: String) -> Result[CapsuleData, String]`
- `capsule_replay_data(data: CapsuleData) -> String`
- `capsule_replay(fs: &FsReadCap, capsule_path: String) -> Result[String, String]`

### `bridge/dir_watcher.oo`
- `dir_watcher_resolve_paths(fs: &FsReadCap, dir: String) -> DirWatchPaths`
- `dir_watcher_state_init(fs: &FsReadCap, dir: String) -> Result[DirWatchState, String]`
- `dir_watcher_poll_tick(fs: &FsReadCap, state: DirWatchState) -> Result[DirWatchTick, String]`
- `dir_watcher_check_once(fs: &FsReadCap, dir: String) -> Result[String, String]`

### `cli/cli_capsule.oo` & `cli/cli_run.oo`
- `cli_run_heal(p: &ProcessCap, fs: &FsReadCap, fw: &FsWriteCap, env: &EnvCap, autopsy_path: String) -> Result[Int, String]`
- `cli_run_pack(fs: &FsReadCap, fw: &FsWriteCap, out_path: String) -> Result[Int, String]`
- `cli_run_replay(fs: &FsReadCap, capsule_path: String) -> Result[Int, String]`
- `cli_run_watch(p: &ProcessCap, fs: &FsReadCap, args: List[String]) -> Result[Int, String]`

## Code Layout & Write Boundaries
- M5 Worker exclusively owns: `autopsy/self_heal.oo`, `autopsy/ANCHOR.oo`, `cli/cli_run.oo` (heal branch), `qa/heal_proof.oo`.
- M6 Worker exclusively owns: `record/flight_capsule.oo`, `record/ANCHOR.oo`, `cli/cli_capsule.oo`, `cli/cli_run.oo` (pack & replay branches), `qa/capsule_proof.oo`.
- M7 Worker exclusively owns: `bridge/dir_watcher.oo`, `bridge/ANCHOR.oo`, `cli/cli_run.oo` (watch branch), `qa/watch_proof.oo`.
- M8 Worker exclusively owns: `scripts/verify_surface.oo`, `scripts/verify.oo`, `scripts/test.oo`, `qa/e2e_blue_ocean_proof.oo`, `qa/heal_proof.oo` (strip standalone main), `qa/capsule_proof.oo` (strip standalone main), `qa/watch_proof.oo` (strip standalone main).
- **STRICT QUARANTINE**: `openOODA/oodac` is NEVER touched by any worker under any circumstances.
- **CONSTITUTIONAL INVARIANTS**: All `.oo` files <= 256 lines, pure `.oo`, 4-element Academy headers (`# Title`, `Logline:`, `Setup:`, `Beats:`), fail-closed error handling, double-run determinism `Run_1 == Run_2`.
