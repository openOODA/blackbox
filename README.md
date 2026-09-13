<div align="center">

<pre>
   ____  ____  ___  ____    ___   ___  ____    _
  / __ \/ __ \/ _ \/ __ \  / _ \ / _ \|  _ \  / \
 / /_/ / /_/ /  __/ / / / | | | | | | | | | |/ _ \
/_____/ .___/\___/_/ /_/  | |_| | |_| | |_| / ___ \
      /_/                   \___/ \___/|____/_/   \_\
</pre>

### openOODA — Primary Systems Language for the AI Era

[openooda.org](https://openooda.org)

</div>

---

## This repo: bb

Flight recorder, crash autopsy engine, and compiler AST diffing subsystem.

```
bb/
├── anchor.oo        Root front door and version surface
├── record/          Telemetry schemas, flight event records, ring buffer
├── autopsy/         Autopsy parser, failure locator, root-cause classifier
├── diff/            AST pass diffing and compiler stage comparator
├── bridge/          Process execution under ProcessCap and MCP bridge
├── cli/             Command line interface (autopsy, diff, trace)
├── qa/              Crash test fixtures, double-run proofs, suite runner
└── docs/            Subsystem specification in ASD-STE100
```

## Purpose

The `bb` CLI reads crash autopsy JSON, dumps live `oodac tokens` output,
auto-records bounded flight capsules for failing spawned processes, heals
sources from autopsy coordinates, and gates CI on token-dump drift.
`run --record <capsule> -- <cmd> [args]` spawns a child under `ProcessCap`
(`sys_exec` captures stdout+stderr, 16MiB bound) and, only on failure, persists
a bounded capsule (command, exit status, truncated output, single overwrite per
path) to the explicit path; successes write nothing.
`diff` locates the compiler from `OODA_COMPILER` or `OODAC_BIN` (no PATH hunt),
runs `tokens`, and exits nonzero when two-file token dumps drift.
`heal [path] [--json]` parses autopsy, applies the patch guard under
`FsWriteCap`, validates via `oodac check`, rolls back on failure, and prints
machine-readable `{"ok":...}` with `--json`.
`inspect` runs live `tokens`, `check`, `emit-c`, or `build` and prints the child text, including gcc diagnostics.
`trace` prints `.blackbox/flight.json` when that file exists and is non-empty; otherwise it prints `ERR`.

## CLI Usage

```sh
# Parse autopsy JSON and output 1-turn agent diagnosis
bb autopsy path/to/autopsy.json

# Run a child; on failure persist a bounded flight capsule (exit nonzero)
bb run --record flight-fail.json -- "$OODA_COMPILER" check path/to/file.oo

# Heal a source defect from autopsy coordinates (machine-readable with --json)
bb heal path/to/autopsy.json --json

# Run oodac tokens on a file (requires OODA_COMPILER or OODAC_BIN)
bb diff path/to/file.oo

# Compare two files; exits nonzero when token dumps drift (CI gate)
bb diff path/to/before.oo path/to/after.oo

# Run a live oodac stage (tokens, check, emit-c, or build)
bb inspect check path/to/file.oo
bb inspect emit-c path/to/file.oo
bb inspect build path/to/file.oo

# Print a written flight log, or ERR if none exists
bb trace
```

## Governance and Constraints

- Pure file laws: source files use only `.oo` and specifications use `.oot`.
- Strict line limit: every `.oo` and `.oot` file contains 256 lines or fewer.
- Academy headers: all `.oo` files contain `# Title`, `Logline`, `Setup`, and `Beats`.
- Import rules: all cross-directory imports use repository-relative paths without parent escapes.

## The Polyrepo Ecosystem

| Repo | Purpose |
|------|---------|
| [openOODA/openOODA](https://github.com/openOODA/openOODA) | Governance, RFCs, laws |
| [openOODA/oodar](https://github.com/openOODA/oodar) | Runtime substrate |
| [openOODA/oodac](https://github.com/openOODA/oodac) | Primary compiler and AST engine |
| [openOODA/std](https://github.com/openOODA/std) | Standard library |
| [openOODA/ooda](https://github.com/openOODA/ooda) | `ooda` workflow driver |
| [openOODA/install](https://github.com/openOODA/install) | Installation scripts and platform packages |
| [openOODA/opm](https://github.com/openOODA/opm) | Package manager |
| [openOODA/catalog](https://github.com/openOODA/catalog) | Official ecosystem package catalog |
| [openOODA/lsp](https://github.com/openOODA/lsp) | Language server |
| [openOODA/mcp](https://github.com/openOODA/mcp) | MCP server |
| [openOODA/bb](https://github.com/openOODA/bb) | Flight recorder and crash autopsy engine |
| [openOODA/website](https://github.com/openOODA/website) | Official openooda.org website source |
| [openOODA/.github](https://github.com/openOODA/.github) | Org profile, shared community files, workflows |

## License

Dual-licensed under your choice of MIT or Apache 2.0. See [LICENSE](LICENSE)
for full terms.
