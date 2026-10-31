# Patch Notes — OpenCode Free Tier Limit Fixes

Date: 2026-10-31
Scope: DeckerGUI repo hardening + OpenCode runtime on this device

## Summary

Applied the OpenCode free-tier limit fixes described in the KrackedDevs article
("OpenCode Free Tier Limit Fixes") to both the DeckerGUI codebase and the local
OpenCode installation. Goal: stop free-tier/IP-gated failures and ensure the
DeckerGUI file directory is not affected by agent/free-tier traffic.

## Part A — DeckerGUI repo changes

### 1. Directory guard (`.gitignore`)
- Added: `.opencode/`, `opencode.json`, `opencode.jsonc`, `.opencodeignore`
- Ensures OpenCode agent state is never committed into the DeckerGUI tree.

### 2. Agent server hardening (`server/server.js`)
- Gated previously open endpoints `GET /api/agent/read`, `/search`, `/files`
  with `requireAgentToken('AGENT.read')`.
- Added `isInsideRoot()` and `isProtectedPath()` containment checks
  (`.git`, `node_modules`, `.env*` are never read/written/exec'd).
- Constrained `exec` `cwd` to the project root; extended command blocklist
  (`rm -rf .git`, `git clean`).
- Skips `.env*` files during directory walks.

### 3. Default model off free tier (`dgui-cli`)
- `src/ade/registry.ts`: online default now resolves to
  `byok/external-provider` when `OPENCODE_DISABLE_FREE_MODELS=1`,
  otherwise `opencode/big-pickle`.
- Updated help text (`src/cli.ts`) and protocol comment (`packages/protocol/src/gate.ts`).
- Documented `OPENCODE_DISABLE_FREE_MODELS=1` in `.env.example`.

## Part B — OpenCode runtime on this device

### Root cause
- Installed `opencode-ai@1.17.18` (OpenCode v1), which no longer supports
  free hosted models.
- Default traffic routed through the IP/client-origin-gated free gateway,
  producing "Free usage limit reached / abusive traffic from this IP".

### Fixes
1. Uninstalled v1 (`opencode-ai`); installed v2 (`@opencode/cli@2.2.5`).
2. Set persistent env var `OPENCODE_DISABLE_FREE_MODELS=1`.
3. Set default model in `~/.config/opencode/opencode.jsonc` to a working
   non-free provider/model: `ollama-cloud/gemma4:31b`.

## Verification

- `opencode --version` → `opencode v2.2.5`
- `opencode run "Reply with exactly: VERIFIED_OK"` → `VERIFIED_OK`
- `node --check server/server.js` → OK
- `cd dgui-cli && npx tsc --noEmit` → OK
- DeckerGUI repo directory untouched by runtime changes (only `~/.config/opencode`
  and user env modified).

## Notes

- Free models remain listed by `opencode models` but are bypassed via the
  configured default model and `OPENCODE_DISABLE_FREE_MODELS=1`.
- For fully unlimited offline use, point `model` at a local Ollama/LM Studio
  endpoint (BYOK), per the article's recommendations.
