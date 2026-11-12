# MVP Requirements

The MVP is the smallest version that shows a real, consent-gated voice conversation with a
Claude-powered twin. It covers Phases 2–3. The avatar, database, and memory are out of scope.

## Functional requirements

| ID | Requirement | Acceptance check |
|---|---|---|
| F1 | User can start and end a voice conversation | Start connects and the agent speaks; End disconnects; status updates in both cases |
| F2 | Voice input and synthesized voice output | User speech is answered with audio |
| F3 | Claude generates the twin's responses | Agent config shows a Claude model; confirmed in conversation details |
| F4 | Twin uses the participant's authorized voice | Agent voice ID equals the verified PVC voice (Phase 3). A built-in voice is used before then. |
| F5 | Visible AI disclosure | Disclosure gate before mic access, persistent AI badge, and the agent's first line states it is an AI |
| F6 | Consent gate | The token endpoint returns 403 when participant consent is not `active` |
| F7 | Connection states | UI shows idle, connecting, connected, speaking/listening, ended, and error |
| F8 | Graceful failures | Mic denied, token failure, and network drop each show a clear message, and the UI can retry |

## Nonfunctional requirements

| Area | Requirement |
|---|---|
| Security | `ELEVENLABS_API_KEY` exists only on the server; nothing secret uses the `NEXT_PUBLIC_` prefix; `.env*` files are gitignored |
| Privacy | No conversation transcripts stored by our app; ElevenLabs retention configured on purpose |
| Accessibility | Controls are keyboard-operable, status is announced to screen readers (`aria-live`), and color contrast is sufficient |
| Maintainability | TypeScript strict mode, participant config kept separate from logic, lint passes |
| Cost | A per-session time limit is set in the agent to cap usage |
| Performance | Latency is measured, not assumed (Phase 6) |

## Out of scope for the MVP

- Avatar or video (Phase 4)
- Database, admin UI, multiple participants (Phase 5)
- Long-term conversational memory
- User accounts and authentication (we will revisit this before any public deployment)
