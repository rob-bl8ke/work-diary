# MCP Server Tech Stack Research

> **Ticket:** [rob-bl8ke/work-diary#6](https://github.com/rob-bl8ke/work-diary/issues/6)  
> **Date:** 2026-09-30  
> **Sources:** modelcontextprotocol.io, github.com/modelcontextprotocol/{typescript-sdk,python-sdk,servers}

---

## Recommendation

**Use the TypeScript/Node.js SDK.**

The single strongest signal: the official MCP `servers` reference repo is **82.2% TypeScript, 16.8% Python** by lines of code ([source](https://github.com/modelcontextprotocol/servers)). Every actively-maintained reference server except `git` is TypeScript. This means more examples to copy from, more community patterns to follow, and a better guarantee that new SDK features land in TypeScript first.

For a locally-run, Obsidian-adjacent tool the `npx` one-liner invocation is also decisive — Obsidian users overwhelmingly already have Node.js; Python venv management adds friction even with `uvx`.

---

## 1. SDK Maturity, Completeness, and Maintenance Activity

| Dimension | TypeScript SDK | Python SDK |
|---|---|---|
| Stars (Sep 2026) | 13.5k | 24.4k |
| Forks | 2.2k | 4.0k |
| Contributors | 193 | 205 |
| Total releases | 175 | 72 |
| Latest release | v2.2.0 (2 days ago) | v2.2.0 (3 weeks ago) |
| Current spec | 2026-07-28 | 2026-07-28 |
| Open issues | 287 | 267 |
| Open PRs | 313 | 192 |

**Sources:** GitHub repo pages for [typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk) and [python-sdk](https://github.com/modelcontextprotocol/python-sdk).

Both SDKs are on v2.2.0 and implement the same `2026-07-28` spec revision — **feature parity at the protocol level is complete**. The TypeScript SDK has significantly more total releases (175 vs 72), indicating a faster release cadence and more incremental iteration. Python has more stars, but that partly reflects Python's larger general developer audience.

**Documentation quality:** Both SDKs have dedicated docs sites with get-started guides, API references, and migration guides:
- TypeScript: [ts.sdk.modelcontextprotocol.io/v2/](https://ts.sdk.modelcontextprotocol.io/v2/)
- Python: [py.sdk.modelcontextprotocol.io/](https://py.sdk.modelcontextprotocol.io/)

Both are of similar quality. The TypeScript SDK uses VitePress; the Python SDK uses MkDocs. The Python SDK has translations in twelve languages ([source](https://github.com/modelcontextprotocol/python-sdk/blob/main/mkdocs.yml)).

**Maintenance:** Both repos show daily commit activity and active PR review. The TypeScript SDK notes v2 is "the stable release line" with v1.x receiving bug fixes for at least 6 months after v2's release ([source](https://github.com/modelcontextprotocol/typescript-sdk#readme)). Python SDK is identical in policy ([source](https://github.com/modelcontextprotocol/python-sdk#readme)).

---

## 2. Runtime and Packaging for a Locally-Run Obsidian-Adjacent Tool

### TypeScript/Node.js

**Zero-install invocation via `npx`:**
```json
{
  "mcpServers": {
    "work-diary": {
      "command": "npx",
      "args": ["-y", "@work-diary/mcp-server"]
    }
  }
}
```
On Windows, wrap with `cmd /c npx` ([source](https://github.com/modelcontextprotocol/servers#using-an-mcp-client)).

Publishing to npm lets any user run the server without a checkout. The standard pattern in the reference repo:
```bash
npx -y @modelcontextprotocol/server-memory
```
([source](https://github.com/modelcontextprotocol/servers#using-mcp-servers-in-this-repository))

**Standalone binary:** Tools like `pkg` or `@vercel/ncc` can bundle the server into a single executable. Not recommended for a personal tool — `npx` already satisfies the "no install" requirement.

### Python

**Zero-install invocation via `uvx`:**
```bash
uvx mcp-server-git
```
([source](https://github.com/modelcontextprotocol/servers#using-mcp-servers-in-this-repository))

Equivalent to `npx` in UX: downloads, caches, and runs in one step. Requires `uv` to be installed, which is not universal.

**`mcp[cli]` extras add `mcp dev`, `mcp run`, and `mcp install` commands** — useful for development but not required for production:
```bash
uv add "mcp[cli]"
# or
pip install "mcp[cli]"
```
([source](https://github.com/modelcontextprotocol/python-sdk#installation))

**Standalone binary:** PyInstaller can produce a single `.exe`/binary, but the output is large (~30 MB) and slower to start than Node.js.

### Verdict for an Obsidian-adjacent tool

`npx` is more universally available than `uvx` in the Obsidian user population — Node.js ships with most developer workstations and is required by Obsidian's plugin ecosystem. `uvx` requires a separate `uv` install. Both are one-liners, but TypeScript has less friction for the target audience.

---

## 3. SDK Feature Comparison

Both SDKs implement the `2026-07-28` MCP spec. The following table summarises what's available in each SDK:

| Feature | TypeScript SDK | Python SDK |
|---|---|---|
| **Tools** | ✅ `server.registerTool()` with Zod/Valibot/ArkType schema | ✅ `@mcp.tool()` decorator; type hints auto-generate JSON Schema |
| **Resources** (file-like data) | ✅ Static + templated URIs | ✅ `@mcp.resource("greeting://{name}")` |
| **Prompts** | ✅ `server.registerPrompt()` | ✅ `@mcp.prompt()` decorator |
| **Sampling** | ✅ (client-side) | ✅ (client-side) |
| **Streamable HTTP transport** | ✅ First-class; middleware packages for Express/Fastify/Hono/Node HTTP | ✅ `mcp run server.py --transport streamable-http` |
| **stdio transport** | ✅ `StdioServerTransport` | ✅ `mcp.run(transport="stdio")` |
| **SSE transport** | ✅ | ✅ |
| **Tool annotations** | ✅ (2026-07-28 spec) | ✅ (2026-07-28 spec) |
| **Schema validation** | Standard Schema (Zod v4, Valibot, ArkType) | Pydantic / Python type hints |
| **Auth helpers** | ✅ OAuth helpers in SDK | Not documented as built-in |
| **MCP Inspector support** | ✅ | ✅ (`mcp dev server.py`) |

**Sources:** TypeScript README ([link](https://github.com/modelcontextprotocol/typescript-sdk#readme)); Python README ([link](https://github.com/modelcontextprotocol/python-sdk#readme)); build-server docs ([link](https://modelcontextprotocol.io/docs/2026-07-28/develop/build-server)).

The SDKs are **feature-equivalent at the protocol level**. The main ergonomic difference is how schemas are declared: TypeScript requires an explicit schema object (Zod is idiomatic), while Python infers JSON Schema from function signatures and type annotations.

---

## 4. Testability and Local Development Tradeoffs

### TypeScript

- **Hot reload:** `nodemon --exec ts-node src/index.ts` or `tsx --watch src/index.ts`. Standard in the Node ecosystem.
- **Unit testing:** Vitest (used by the SDK itself) or Jest. MCP server logic is just functions — easy to unit test.
- **MCP Inspector:** `npx @modelcontextprotocol/inspector` connects to any stdio server.
- **Debugging:** `--inspect` flag on Node.js; VS Code debugger attaches cleanly.
- **Type safety:** TypeScript types enforce correct tool return shapes at compile time, catching protocol errors before runtime.
- **Build step required:** `tsc` compiles before running unless using `ts-node`/`tsx`. Minor friction.

### Python

- **Hot reload:** `mcp dev server.py` (MCP CLI) wraps the server and auto-restarts on file change. This is a first-class DX feature the TypeScript SDK does not have built-in ([source](https://github.com/modelcontextprotocol/python-sdk#installation)).
- **Unit testing:** `pytest` with `asyncio`. The SDK uses `asyncio` throughout.
- **MCP Inspector:** Also compatible.
- **Debugging:** `pdb`, `debugpy`, or VS Code Python debugger.
- **No build step:** `python server.py` just runs. No compile step.
- **Async pitfalls:** The SDK is fully async (`asyncio`). Blocking the event loop is a known failure mode; the SDK now detects this in tests ([source](https://github.com/modelcontextprotocol/python-sdk/blob/main/tests/)).

### Verdict

Python wins on "zero build step" and the built-in `mcp dev` hot-reload. TypeScript wins on type safety and the broader JS tooling ecosystem (Vitest, ts-node, tsx). For a solo developer maintaining a personal work-diary tool, the Python DX is slightly more frictionless day-to-day, but the difference is not decisive.

---

## 5. Community Examples and Real-World Server Distribution

### Official reference servers repo (`modelcontextprotocol/servers`)

- **90.8k stars, 11.7k forks, 928 contributors** ([source](https://github.com/modelcontextprotocol/servers))
- **Language split: TypeScript 82.2%, Python 16.8%** ([source](https://github.com/modelcontextprotocol/servers))

Actively maintained reference servers as of Sep 2026:

| Server | Language |
|---|---|
| Everything (test/demo) | TypeScript |
| Fetch | TypeScript |
| Filesystem | TypeScript |
| Memory (knowledge graph) | TypeScript |
| Sequential Thinking | TypeScript |
| Time | TypeScript |
| Git | Python |

The Git server is the only actively-maintained Python reference server. All others are TypeScript. ([source](https://github.com/modelcontextprotocol/servers#reference-servers))

### Community and MCP Registry

The [MCP Registry](https://registry.modelcontextprotocol.io/) is the canonical list of published servers. The servers repo README notes: "If you are looking for a list of MCP servers, you can browse published servers on the MCP Registry." Qualitative observation from the reference servers, quickstart resources, and community discussions: the majority of production-grade, actively-maintained open-source MCP servers are TypeScript. Python is common for data-science-adjacent servers and simpler scripts.

### Anthropic's own tooling

Anthropic's [Claude Code](https://github.com/anthropics/claude-code) and the MCP Inspector are TypeScript. The canonical quickstart examples on [modelcontextprotocol.io](https://modelcontextprotocol.io/docs/2026-07-28/develop/build-server) are written first in Python, then TypeScript — but the reference implementations in the servers repo default to TypeScript.

---

## Summary

| Question | Answer |
|---|---|
| Recommended SDK | **TypeScript/Node.js** |
| Packaging | `npx` one-liner via npm |
| Feature parity | Both SDKs implement 2026-07-28 spec identically |
| Best local DX | Python (`mcp dev` hot reload); TypeScript is close with `tsx --watch` |
| Community server majority | TypeScript (82.2% of reference server code) |
| Key risk (TypeScript) | Build step required; slightly more boilerplate |
| Key risk (Python) | `uvx` is less universal; asyncio pitfalls |

The recommendation is TypeScript primarily because of the **community example density** — when building against the MCP spec the first time, having 5× more reference implementations to read is a concrete advantage.
