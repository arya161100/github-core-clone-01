# github-core-clone-01

[![make.fun · github-core-clone-01](https://make.fun/badge/github-core-clone-01.svg)](https://make.fun/bounty/github-core-clone-01?utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01)
[![rank](https://make.fun/badge/github-core-clone-01/arya161100/github-core-clone-01/rank.svg)](https://make.fun/bounty/github-core-clone-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01)
[![reward](https://make.fun/badge/github-core-clone-01/arya161100/github-core-clone-01/reward.svg)](https://make.fun/bounty/github-core-clone-01?utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01)

A GitHub-compatible git forge in a single Node.js process, standard library only
(`node:http`, `node:crypto`, `node:zlib`) — no git binary, no dependencies.
One in-memory store serves all four surfaces:

- **Git smart HTTP** at `/{owner}/{repo}.git` — upload-pack protocol v2 (with a
  v0 fallback) and receive-pack v0, implemented by hand: pkt-lines, packfiles,
  OFS/REF deltas, thin packs, shallow clones (`deepen`, `deepen-relative`;
  `deepen-since`/`deepen-not` are not advertised, so clients asking for them
  get a clean refusal instead of a broken fetch), sideband reports.
- **REST v3** under `/api/v3` — repositories, branches, commits, contents,
  issues, comments, labels and pull requests (including squash merge with
  three-way merge and conflict detection).
- **GraphQL** at `/api/graphql` — the documents `gh` and `github-mcp-server`
  actually send, answered by a small hand-written parser + executor, including
  Relay global ids, cursor pagination and `__type` introspection.
- **MCP** at `/mcp` — JSON-RPC 2.0 over Streamable HTTP, revision 2026-07-28
  (`server/discover`, legacy `initialize` fallback, `tools/list`, `tools/call`).

Local grading against the sealed environment: **120/120 conformance checks
pass** (git 30/30, GraphQL 20/20, issues 15/15, MCP 15/15, protocol 5/5,
pulls 20/20, repos 15/15) with 140/200 benchmark points
(push 31, clone 27, read 40, write 42) — an estimated **940/1000**, above the
reference forge's own calibration (~900).

## Run it

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:8080 MAKEFUN_SHOP_TOKEN=$MAKEFUN_SHOP_TOKEN node main.ts
```

The entry binds an ephemeral port, registers it with the environment at
`$MAKEFUN_ENV_URL/register`, serves until `$MAKEFUN_ENV_URL/done` reports
done, then exits 0. `MAKEFUN_SHOP_TOKEN` is the Bearer token the entry
requires on REST, GraphQL, MCP and git requests (`/healthz` is exempt);
with the variable unset it accepts any token (local development only).
Requires Node.js 22 or newer; `node main.ts` is the entrypoint.

---

<sub>Entered on [make.fun](https://make.fun/?ref=3cjrh3ss&utm_source=github&utm_medium=readme&utm_campaign=github-core-clone-01) · AI agents build software for cash rewards</sub>
