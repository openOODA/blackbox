# Agent Instructions for blackbox

You are operating within the openOODA polyrepo. The blackbox package is the
agent-native execution flight recorder and crash autopsy engine. It captures
process telemetry, intercepts unhandled crash signals (SIGSEGV, SIGABRT,
SIGBUS, SIGILL), and diffs compiler intermediate representations across
passes. Your execution must be rigorous, deeply skeptical, and strictly
bound by the repository's governance laws (`openOODA/RULES.oot` and
`openOODA/FLOOR.oot`).

## 1. Zero Trust & The Double-Run Law
- **Falsify, never confirm:** A test that always passes provides no proof.
  Hostile negative-trust tests must be run TWICE in fresh processes and
  produce identical results to be considered verified.
- **Substratum Integrity:** The zero-heap C sensor in oodar executes on an
  alternate signal stack. Never bypass it.
- **Crash means data, not blame:** An autopsy JSON is evidence. Diagnose from
  the stack/RIP/caps fields, never from the message string alone.

## 2. Services for Speed (No Shortcuts)
- **Do not blindly `grep` the tree.** Use the blackbox CLI directly.
- **You MUST use the native blackbox subcommands:**
  - `blackbox autopsy [path]` — parse crash telemetry, output 1-turn
    diagnosis.
  - `blackbox diff <file.oo>` — diff AST across compiler passes
    (lex, ast, check, emit).
  - `blackbox trace <file.oo>` — record and dump execution flight events.
  - `blackbox mcp --stdio` — start the standalone JSON-RPC 2.0 MCP server
    (4 tools: autopsy, diff, diff_em, trace).
  - `blackbox heal [path]` — apply the repair recipe from the autopsy.
  - `blackbox pack / replay` — bundle signed flight capsules (.bb).
  - `blackbox run --record <capsule> -- <cmd> [args]` — bounded flight
    capture on a child process.
  - `blackbox watch [dir]` — directory monitor for crash artifacts.

## 3. Strict Repository Compliance
- **Pure Files:** Only `.oo` and `.oot` files are permitted for logic
  (RULES.oot §1.14). Version is `0.0.3` in VERSION and `blackbox_version()`.
- **Line Limits:** Absolute maximum of 256 lines per file.
- **Academy Headers:** All `.oo` files must begin with the exact 4-element
  Academy header.
- **Seven Subsystem Directories:** `record/`, `autopsy/`, `diff/`, `bridge/`,
  `cli/`, `qa/`, `docs/`. ANCHOR.oo is the front door.

## 4. Commit Hygiene
- **One Repo, One Commit:** Never bundle changes across multiple
  repositories in a single commit.
- **Docs in the Same Commit:** Any behavioral change must be accompanied
  by the corresponding `docs/` update in the very same commit. Bump
  VERSION when the surface changes.
- **Tag = VERSION:** This repo's tag IS the version (RFC-0006).
