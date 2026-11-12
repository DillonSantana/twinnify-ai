# Responsible AI Design

Each protection below is an **application behavior** with a planned test, not just a policy statement.

## Two layers of permission

| Layer | Question | Decided by | Frequency |
|---|---|---|---|
| 1. Participant consent | May this participant's twin exist, and for which uses? | Participant | Once, until revoked |
| 2. User access approval | May this specific user talk to the twin? | Participant | Once per user, until revoked |

The participant does **not** approve individual conversations. The token route checks both layers
on every Start: consent must be `active` **and** the user must be on the participant's approved
list and not revoked. Revoking a user blocks their next session without affecting anyone else.

- **MVP (local, operator only):** layer 1 only.
- **Before any other user or any deployment (Phase 5):** layer 2 is required. This needs user
  identity (login) and an approved-users store. At first the operator records approvals the
  participant gives; later the participant may get an admin page to approve or revoke users directly.

## Controls

| Requirement | Enforcement in the app | Phase | Test |
|---|---|---|---|
| Consent before activation | Participant config has `consent.status`. The token route returns `403` unless it is `active` | 2 | Unit test: `revoked`/`pending` → 403 |
| Consent scope | Config lists permitted uses (`voice`, `likeness`, `publicDemo`). Features check scope before enabling (e.g. no avatar without `likeness`) | 3–5 | Unit test per scope flag |
| Approved users | Token route refuses users not on the participant's approved list, or revoked from it. Approval is once per user, not per conversation | 5 (required before deployment) | Unit test: unknown → 403, approved → token, revoked → 403 |
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

// Phase 5: stored in a database, not in the repository (contains personal data).
interface ApprovedUser {
  participantId: string;
  userId: string;             // from the login provider
  status: "approved" | "revoked";
  approvedAt: string;         // when the participant approved this user
  revokedAt?: string;
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
