# OpenCode Free Tier Limit Fixes

![GitHub stars](https://img.shields.io/github/stars/ctaxnagomi/opencode-free-tier-limit-fixes?style=flat-square)
![GitHub license](https://img.shields.io/github/license/ctaxnagomi/opencode-free-tier-limit-fixes?style=flat-square)
![OpenCode](https://img.shields.io/badge/OpenCode-v2.2.5-blue?style=flat-square)
![Status](https://img.shields.io/badge/status-fixed-success?style=flat-square)
![Contributor](https://img.shields.io/badge/contributor-ctaxnagomi-orange?style=flat-square)

OpenCode free tier limit fixes and a two-devices-on-the-same-network workaround.

## Problem

OpenCode's free tier is enforced by an SDK-gated proxy that prioritizes IP and client-origin restrictions over strict per-account quotas. As a result, users can hit "Free usage limit reached" after only 1-3 prompts, especially when two devices share the same public IP.

## Fixes

1. Upgrade to OpenCode v2 (`npm uninstall -g opencode-ai && npm install -g @opencode/cli`).
2. Set `OPENCODE_DISABLE_FREE_MODELS=1`.
3. Use BYOK / external providers (Ollama Cloud, OpenRouter, Gemini, Groq, etc.).
4. Separate public IPs for each device, or use local models.
5. Network hygiene: disable VPN/proxy, clear credentials, re-authenticate.

## Two devices on the same network

See the article: `OpenCode_Free_Tier_Limit_Fixes_Comprehensive_Solutions_[FIXED_2026].md`

## Patch notes

See `PATCH_NOTES_OPENCODE_FREE_TIER.md`.

## Contributor

ctaxnagomi
