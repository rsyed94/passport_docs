# Passport context-efficiency benchmark

Tokenizer: exact `o200k_base` encoding · 3 measured repetitions after warmup.

| scenario | surface | tool-definition tokens | total task-context tokens | MCP body KB (request + response) | p50 ms | p95 ms |
|---|---|---:|---:|---:|---:|---:|
| small (8 tools, 1 KB result) | flat_mcp | 1313 | 1746 | 8.4 | 17.41 | 18.16 |
| small (8 tools, 1 KB result) | compact_mcp | 1026 | 2465 | 11.8 | 19.89 | 21.02 |
| small (8 tools, 1 KB result) | passport_cli | 0 | 1165 | 17.8 | 336.69 | 342.92 |
| medium (48 tools, 8 KB result) | flat_mcp | 4933 | 7356 | 32.3 | 20.71 | 23.32 |
| medium (48 tools, 8 KB result) | compact_mcp | 1106 | 4825 | 21.6 | 19.68 | 20.1 |
| medium (48 tools, 8 KB result) | passport_cli | 0 | 3389 | 28.1 | 350.99 | 356.59 |
| large (160 tools, 32 KB result) | flat_mcp | 15069 | 24203 | 104.2 | 37.69 | 40.26 |
| large (160 tools, 32 KB result) | compact_mcp | 1212 | 11680 | 49.7 | 27.17 | 27.55 |
| large (160 tools, 32 KB result) | passport_cli | 0 | 10086 | 56.8 | 361.85 | 365.78 |

## Context ratios versus flat MCP

| scenario | compact definitions | compact total | CLI total |
|---|---:|---:|---:|
| small | 78.1% | 141.2% | 66.7% |
| medium | 22.4% | 65.6% | 46.1% |
| large | 8% | 48.3% | 41.7% |

## Interpretation

- Tool-definition and task-context tokens are exact for the o200k_base text encoding, but host-specific MCP wrappers and any generic shell-tool schema are excluded equally. They are not a billing estimate for every model.
- MCP wire figures are JSON HTTP body bytes only. Headers are excluded because bearer length is noise and credentials must never enter output. CLI auth exchange bytes are recorded separately in JSON.
- CLI context assumes the agent already knows the stable Passport command contract. One-time installation/help text is excluded; every per-task command and stdout byte is included.
- Latency includes localhost gateway/upstream work. CLI includes two Node subprocesses, two credential exchanges, and two MCP sessions. Latency is observational and deliberately has no pass/fail threshold.
- Compact/CLI search returns at most ten matching definitions. This task searches across the permitted fleet, selects the namespaced result, and performs one governed call.

Canaries: 33/33 passed.
