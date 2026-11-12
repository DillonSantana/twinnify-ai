# Risks & Dependencies

| Risk | Impact | Mitigation |
|---|---|---|
| No real-time ElevenLabs avatar API confirmed (ElevenLabs Avatars appears to make pre-rendered video only) | Phase 4 may need a third-party provider (e.g. HeyGen LiveAvatar, Anam) | Verify in Phase 4; fallback is an audio-reactive visual |
| Avatar needs separate likeness consent | Avatar blocked until consent covers it | `consent.scope.likeness` flag enforced in code |
| PVC not yet trained/verified | Custom voice unavailable | Use a built-in voice for Phase 2; swap the voice ID in Phase 3 |
| Exact Claude model list in ElevenLabs unverified | Wrong model ID or deprecated model | Query the agent's supported LLMs once the agent exists |
| ElevenLabs docs could not be read from the dev environment | Some details came from secondary sources | Re-verify against official docs before relying on them |
| Usage cost (per-minute agent + LLM) | Unexpected bills | Session time limit, no public link without auth, record real costs in Phase 6 |
| Browser mic requires HTTPS or localhost | Mic fails on plain-HTTP deployments | Deploy only over HTTPS |
| SDK churn (`@11labs/*` → `@elevenlabs/*`, fast releases) | Breaking changes | Pin exact versions; read changelogs before upgrading |
| Next.js 16 has breaking changes vs. older tutorials | Outdated examples fail | Use the bundled docs in `node_modules/next/dist/docs/` |
| API key leakage | Account abuse | Server-only key, restricted-scope key, gitignored env files |

## External dependencies

- ElevenLabs account with Agents access, plus PVC
- Node.js 20+
- Hosting with HTTPS (decided in Phase 6)
