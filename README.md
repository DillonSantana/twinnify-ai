# Twinnify AI — Digital Twin AI

AI-powered conversations with a **consenting** person's digital twin, built on an architecture that can be reused for any participant who gives consent.

> **Status:** Phase 1 (repository & architecture). There is no working conversation feature yet.
> This README only describes what exists and has been verified. Planned features are labelled as planned.

## What this is

Digital Twin AI is a web application where a user can hold a real-time voice conversation with an
AI representation of a real participant who has given documented consent. The twin speaks with an
authorized synthetic voice, uses Anthropic Claude for its conversational responses, and is
**always clearly disclosed as an AI simulation**. It never presents itself as the real person.

## Architecture at a glance

```
Browser (Next.js UI) ──short HTTPS request──► Next.js route handler (our server)
        │                                       - checks participant consent is active
        │                                       - holds the ElevenLabs API key
        │                                       - returns a short-lived conversation token
        │
        └──── WebRTC audio (direct) ────────► ElevenLabs Agent (managed)
                                                speech-to-text → Claude → text-to-speech
```

See [docs/architecture.md](docs/architecture.md) for the full explanation.

## Tech stack

| Layer | Choice | Why |
|---|---|---|
| Frontend + server | Next.js (App Router), React, TypeScript | One project with server-side route handlers that keep API keys secret |
| Styling | Tailwind CSS | Fast, consistent styling |
| Voice pipeline | ElevenLabs Agents | Managed speech recognition, turn-taking, and synthesis |
| Conversational model | Anthropic Claude, selected inside the ElevenLabs agent | Simplest supported integration |
| Database | None yet | Added only when a requirement needs one (Phase 5) |

The reasons for each choice are recorded in [docs/decisions/](docs/decisions/).

## Getting started

Requires Node.js 20+.

```bash
npm install
cp .env.example .env.local   # then fill in values (never commit .env.local)
npm run dev
```

Open http://localhost:3000.

Other scripts: `npm run lint`, `npm run build`.

## Documentation

- [Architecture](docs/architecture.md)
- [MVP requirements](docs/mvp-requirements.md)
- [Responsible AI design](docs/responsible-ai.md)
- [Risks & dependencies](docs/risks.md)
- [Architecture decision records](docs/decisions/)
- [Participant consent template](docs/consent/participant-consent-template.md)

## Responsible AI

This project uses a synthetic voice, and possibly a synthetic likeness, of a real person. The app
enforces the following protections in code:

- the server will not start a conversation unless consent is recorded as active,
- the user sees a disclosure screen before the microphone turns on,
- an AI badge stays visible throughout the conversation.

Details are in [docs/responsible-ai.md](docs/responsible-ai.md).

## Roadmap

1. ✅ Repository & architecture
2. Basic conversational AI
3. Custom voice & persona
4. Avatar integration
5. Reusable participant architecture & consent management
6. Performance, testing & deployment
7. Portfolio presentation

## Authorship

This project is developed by Dillon Santana with AI assistance (Claude Code). AI-assisted
contributions are noted in commit history.
