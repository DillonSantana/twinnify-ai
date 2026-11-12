# ADR 0001: Next.js as the full-stack framework

**Status:** Accepted, Phase 1

## Context
We need a React + TypeScript UI and a small server component that keeps the ElevenLabs API key
secret and mints short-lived conversation tokens.

## Options
1. React (Vite) + Express
2. React (Vite) + Fastify
3. Next.js (App Router) with route handlers

## Decision
Next.js with route handlers.

## Rationale
- One codebase, one deploy, no CORS configuration.
- Route handlers run only on the server, so the API key never reaches the browser.
- Our server is not in the real-time audio path (audio goes browser ↔ ElevenLabs over WebRTC),
  so serverless limits on long-lived connections don't affect us.

## Consequences
- If we later need a long-running service (for example a Custom LLM proxy or a memory service),
  that may be a separate Fastify/Express service.
- Next.js 16 has breaking changes compared with older tutorials, so we use the bundled docs.
