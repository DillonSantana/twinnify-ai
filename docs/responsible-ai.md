# Responsible AI Design

Each protection below is an **application behavior** with a planned test, not just a policy statement.

## Controls

| Requirement | Enforcement in the app | Phase | Test |
|---|---|---|---|
| Consent before activation | Participant config has `consent.status`. The token route returns `403` unless it is `active` | 2 | Unit test: `revoked`/`pending` → 403 |
| Consent scope | Config lists permitted uses (`voice`, `likeness`, `publicDemo`). Features check scope before enabling (e.g. no avatar without `likeness`) | 3–5 | Unit test per scope flag |
| Revocation | Setting `consent.status = "revoked"` blocks new sessions immediately (in Phase 5 this becomes a database flag plus an admin action) | 2 / 5 | Revoke, then confirm Start fails |
| AI disclosure | Disclosure gate before mic permission, persistent "AI simulation" badge, and the agent's first message states it is an AI | 2–3 | UI test + manual listen |
| No impersonation | System prompt forbids claiming to be the real person and requires the twin to admit it is an AI when asked | 3 | Scripted prompts that try to make it claim otherwise |
| Secret protection | API key server-only; voice and agent IDs come from env vars; agent requires authentication (no public agent link) | 2 | Check the client bundle for the key; check the agent auth setting |
| Data minimization | Our app stores no transcripts or audio. ElevenLabs conversation retention set deliberately | 3 | Review agent retention settings |
| Deletion | Runbook below; automated in Phase 5 | 5 | Dry run |

## Participant record (planned shape, no secrets)

```ts
type ConsentStatus = "pending" | "active" | "revoked";

interface ParticipantConfig {
  id: string;                 // e.g. "eni"
  displayName: string;        // shown with an "AI simulation" label
  consent: {
    status: ConsentStatus;
    scope: { voice: boolean; likeness: boolean; publicDemo: boolean };
    recordedAt: string;       // ISO date the signed agreement was received
  };
  agentIdEnvVar: string;      // name of the env var holding the agent ID, not the ID itself
}
```

## Deletion runbook (manual until Phase 5)

1. Set `consent.status` to `"revoked"` and deploy. New sessions are blocked at this point.
2. In ElevenLabs, delete the participant's agent, which also removes its conversation history.
3. In ElevenLabs, delete the participant's PVC voice.
4. Delete any avatar assets from the avatar provider (Phase 4+).
5. Remove the participant config and env vars; rotate the API key if it might have been exposed.
6. Delete the locally stored training audio, if any.
7. Record the deletion date with the consent records (kept outside this repository).

## Current consent status: participant "Eni"

| Item | Status |
|---|---|
| Written consent for voice use | ✅ Received (stored privately, not in repo) |
| Public demonstration | ❓ To confirm |
| Likeness (photo/video) for avatar | ❓ To confirm |
| Provider voice verification (ElevenLabs PVC) | ⏳ Not yet completed |

Until the PVC is trained and verified, development uses a built-in ElevenLabs voice.
