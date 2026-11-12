# ADR 0002: Use Claude through ElevenLabs' native LLM setting

**Status:** Accepted, Phase 1. The model list is still to be verified in Phase 2.

## Context
Claude should power the twin's responses. ElevenLabs Agents can either call a supported LLM
natively or call a "Custom LLM" endpoint (OpenAI Chat Completions-compatible) that we host.

## Decision
Select a Claude model in the agent's native LLM setting.

## Rationale
- No extra server sits in the turn loop, which means one fewer network hop and one fewer failure point.
- No Anthropic API key to manage. LLM usage is billed through ElevenLabs.
- The persona and guardrails live in the agent's system prompt.

## Consequences
- Less control over each LLM request. Advanced memory or retrieval may later require the Custom LLM route.
- Start with a fast model (e.g. Haiku) and compare against Sonnet using measured latency and quality in Phase 6.

## Note: two different uses of Claude
- **Development assistant:** Claude Code helps write this codebase. It is not part of the running app.
- **Runtime model:** the Claude model selected in the ElevenLabs agent generates the twin's replies.
