<div align="center">

<pre>
   ____  ____  ___  ____    ___   ___  ____    _
  / __ \/ __ \/ _ \/ __ \  / _ \ / _ \|  _ \  / \
 / /_/ / /_/ /  __/ / / / | | | | | | | | | |/ _ \
/_____/ .___/\___/_/ /_/  | |_| | |_| | |_| / ___ \
      /_/                   \___/ \___/|____/_/   \_\
</pre>

### openOODA — Sovereign Systems Language for the AI Era

[openooda.org](https://openooda.org)

</div>

---

## This repo: blackbox

Flight recorder, crash autopsy engine, and compiler AST diffing subsystem.

```
blackbox/
├── ANCHOR.oo        Root front door and version surface
├── record/          Telemetry schemas, flight event records, ring buffer
├── autopsy/         Autopsy parser, failure locator, root-cause classifier
├── diff/            AST pass diffing and compiler stage comparator
├── bridge/          Process execution under ProcessCap and MCP bridge
├── cli/             Command line interface (autopsy, diff, trace)
├── qa/              Crash test fixtures and double-run invariant proofs
├── scripts/         Build, test, and verification automation
└── docs/            Subsystem specification in ASD-STE100
```

## Purpose

The `blackbox` CLI reads crash autopsy JSON and dumps live `oodac tokens` output.
This repo does not write `.blackbox/autopsy.json` or a flight log; autopsy is fixture-driven.
`diff` locates the compiler from `OODA_COMPILER` or `OODAC_BIN` (no PATH hunt) and runs `tokens`.
`inspect` runs live `tokens`, `check`, `emit-c`, or `build` and prints the child text, including gcc diagnostics.
`trace` prints `.blackbox/flight.json` when that file exists and is non-empty; otherwise it prints `ERR`.

## CLI Usage

```sh
# Parse autopsy JSON and output 1-turn agent diagnosis
blackbox autopsy path/to/autopsy.json

# Run oodac tokens on a file (requires OODA_COMPILER or OODAC_BIN)
blackbox diff path/to/file.oo

# Run a live oodac stage (tokens, check, emit-c, or build)
blackbox inspect check path/to/file.oo
blackbox inspect emit-c path/to/file.oo
blackbox inspect build path/to/file.oo

# Print a written flight log, or ERR if none exists
blackbox trace
```

## Governance and Constraints

- Pure file laws: source files use only `.oo` and specifications use `.oot`.
- Strict line limit: every `.oo` and `.oot` file contains 256 lines or fewer.
- Academy headers: all `.oo` files contain `# Title`, `Logline`, `Setup`, and `Beats`.
- Import rules: all cross-directory imports use repository-relative paths without parent escapes.

## The Polyrepo Ecosystem

| Repo | Purpose |
|------|---------|
| [openOODA/openOODA](https://github.com/openOODA/openOODA) | Governance, RFCs, and constitutional invariants |
| [openOODA/ooda](https://github.com/openOODA/ooda) | Unified developer workflow driver |
| [openOODA/oodac](https://github.com/openOODA/oodac) | Sovereign compiler and AST engine |
| [openOODA/oodar](https://github.com/openOODA/oodar) | Low-level C runtime substrate |
| [openOODA/std](https://github.com/openOODA/std) | Standard library |
| [openOODA/mcp](https://github.com/openOODA/mcp) | Model Context Protocol server |
| [openOODA/opm](https://github.com/openOODA/opm) | Cryptographic package manager |
| [openOODA/lsp](https://github.com/openOODA/lsp) | Language Server Protocol daemon |
| [openOODA/catalog](https://github.com/openOODA/catalog) | Official ecosystem package catalog |
| [openOODA/website](https://github.com/openOODA/website) | Official openOODA website source |
| [openOODA/install](https://github.com/openOODA/install) | Installation scripts and platform packages |
| [openOODA/.github](https://github.com/openOODA/.github) | Shared GitHub workflows and community health |
| [openOODA/blackbox](https://github.com/openOODA/blackbox) | Operational Logistics: Agent-native execution flight recorder and crash autopsy engine |

## License

Dual-licensed under your choice of MIT or Apache 2.0. See [LICENSE](LICENSE)
for full terms.
