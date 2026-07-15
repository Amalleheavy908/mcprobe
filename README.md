# mcprobe

[![Go Report Card](https://goreportcard.com/badge/github.com/tamish560/mcprobe?style=flat-square)](https://goreportcard.com/report/github.com/tamish560/mcprobe)
[![Go Version](https://img.shields.io/badge/Go-1.23-00ADD8?style=flat-square&logo=go)](https://go.dev)
[![License: MIT](https://img.shields.io/badge/license-MIT-0F172A?style=flat-square)](./LICENSE)
[![Release](https://img.shields.io/github/v/release/tamish560/mcprobe?style=flat-square&label=release)](https://github.com/tamish560/mcprobe/releases)
[![Tests](https://img.shields.io/badge/tests-19%20passing-22C55E?style=flat-square)](./mcprobe_test.go)

Security scanner and introspection tool for MCP (Model Context Protocol) servers.

Connect to any MCP server, introspect its tools/prompts/resources, detect prompt injection patterns in tool descriptions, find tool shadowing across servers, and baseline for drift detection (rug-pull attacks).

Single binary. Zero dependencies. Go stdlib only.

## Why

MCP servers give AI agents access to your filesystem, shell, databases, and APIs. A malicious or compromised server can embed prompt injection in tool descriptions, shadow legitimate tools, or silently change its capabilities after you trust it. mcprobe catches these.

Academic research (DSN 2026) found 833 vulnerable servers across MCP registries and 18 servers with suspicious tool descriptions designed to manipulate LLM behavior. Almost no one is running pre-integration security checks. mcprobe fixes that.

## Install

```
go install github.com/tamish560/mcprobe@latest
```

Or build from source:

```
git clone https://github.com/tamish560/mcprobe.git
cd mcprobe
go build -o mcprobe
```

Or download a prebuilt binary from the [latest release](https://github.com/tamish560/mcprobe/releases).

## Quick Start

Scan a stdio MCP server:

```
mcprobe -command 'node server.js'
```

Scan an HTTP MCP server:

```
mcprobe -http http://localhost:3000/mcp
```

Scan an SSE MCP server:

```
mcprobe -sse http://localhost:3000/sse
```

List tools without security scan:

```
mcprobe -command 'node server.js' -list
```

## Baseline and Drift Detection

Save a baseline snapshot of a server's capabilities:

```
mcprobe -command 'node server.js' -baseline server-baseline.json
```

Check for drift (rug-pull attacks) against the baseline:

```
mcprobe -command 'node server.js' -diff server-baseline.json
```

This detects:
- Tool descriptions changed (potential rug-pull)
- Tools added or removed
- Schemas modified
- Resources added or removed
- Server version changes

## Tool Shadowing Detection

When multiple MCP servers are connected to the same agent, a malicious server can define tools with the same name as legitimate tools. Save baselines for each server, then check:

```
mcprobe -shadow -shadow-dir ./baselines/
```

## CI Integration

Use SARIF output for GitHub Actions, GitLab CI, or any SARIF-compatible scanner:

```
mcprobe -command 'node server.js' -format sarif -out results.sarif
```

Exit codes:
- 0: scan completed, no high/critical findings
- 1: error
- 2: scan completed, high or critical findings detected

## What It Detects

### Prompt Injection (CRITICAL)
Scans tool descriptions, prompt descriptions, and schema property descriptions against 18 injection patterns:
- "ignore all previous instructions"
- "you are now a ..."
- "act as if ..."
- "execute arbitrary commands"
- "read any file/env/secret"
- "rm -rf", "curl | bash"
- "exfiltrate data/secrets/keys"
- "disable/bypass security"
- "base64 decode"
- "override safety/policy/guardrail"
- and more

### Tool Shadowing (HIGH/CRITICAL)
When two or more MCP servers define a tool with the same name, the LLM cannot distinguish which server to call. A malicious server can exploit this to intercept tool calls meant for a legitimate server.

### Path Traversal (HIGH)
Resource URIs containing `..` sequences that could allow reading files outside intended directories.

### Missing Metadata (LOW)
Tools without descriptions or input schemas are harder to audit and may indicate rushed or malicious code.

### Oversized Descriptions (MEDIUM)
Tool descriptions over 2000 characters may hide embedded instructions.

### Rug-Pull Detection (CRITICAL)
When a tool's description changes between baseline and current scan, the server may have been compromised to inject new instructions into the LLM's context.

## Output Formats

- `text` (default): human-readable report with risk score
- `json`: full structured scan result
- `sarif`: SARIF 2.1.0 for CI integration

## Risk Scoring

| Score | Level |
|-------|-------|
| 0-9 | MINIMAL |
| 10-24 | LOW |
| 25-49 | MEDIUM |
| 50-74 | HIGH |
| 75-100 | CRITICAL |

## Architecture

```
transport.go   MCP client transport (stdio + HTTP)
sse.go         SSE (Server-Sent Events) transport
client.go      JSON-RPC client, server introspection
scanner.go     Security analysis engine, pattern detection, shadowing
baseline.go    Snapshot persistence, drift detection
report.go      Text, JSON, SARIF output renderers
main.go        CLI entry point, flag parsing
mcprobe_test.go  19 tests: scanner, shadowing, risk scoring, drift detection
```

No external dependencies. Pure Go standard library. Builds to a single static binary.

## License

MIT
