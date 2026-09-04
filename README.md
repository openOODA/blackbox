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

The `blackbox` subsystem provides execution flight recording and crash autopsy.
When an executable crashes, the runtime substrate writes `.blackbox/autopsy.json`.
The autopsy engine parses this record and produces a compact 1-turn root-cause diagnosis.
The diff engine runs compiler stages to show semantic AST differences.

## CLI Usage

```sh
# Parse crash autopsy and output 1-turn agent diagnosis
ooda blackbox autopsy [.blackbox/autopsy.json]

# Run compiler stages and display semantic AST diffs
ooda blackbox diff path/to/file.oo

# Inspect ring buffer flight telemetry trace
ooda blackbox trace
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
| [openOODA/blackbox](https://github.com/openOODA/blackbox) | Flight recorder and crash autopsy subsystem |

## License

Dual-licensed under your choice of MIT or Apache 2.0. See [LICENSE](LICENSE)
for full terms.
