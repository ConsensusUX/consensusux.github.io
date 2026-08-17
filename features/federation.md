---
title: Federation
parent: Features
nav_order: 5
layout: default
---

# Federation

> This feature is planned. No production code has been written.
{: .planned }

Federation enables multiple groups to form relationships with each other and share decision-making through shared chats. The architecture is deliberately flat: the same consensus mechanics that govern decisions within a group also govern decisions between groups. Federation is not a separate tier of authority — it is horizontal organizing at scale.

---

## Overview

A single ConsensusUX group is a bounded, self-governing community. Federation extends this by allowing groups to:

- Establish recognized relationships with other groups
- Create shared chats where inter-group proposals can be discussed and evaluated
- Coordinate on matters of common concern without either group subordinating to the other

> Federation (the relationship between groups at the same level) mirrors subgroup structure (the relationship between a group and a nested subgroup below it) using the same technical architecture. There is no special inter-group admin role.
{: .note }

---

## Group Relationships

Group relationships are managed from a **Group Relationships tab** accessible from the main chat screen. Relationships exist in three states:

| State | Label | Description |
|-------|-------|-------------|
| Initiated contact, not yet vouched | Connected (provisional) | Another group has made contact; mutual vouching is not yet complete |
| Mutual vouching complete | Federated | Both groups have vouched for each other and may create shared chats |
| Formerly federated | De-federated | The relationship has been terminated by either group |

---

## Federation Process

### Step 1 — Discovery

Groups discover each other through a mechanism that is currently an **open question** (see below). Possible mechanisms include: a public directory, a referral from a shared member, or a known contact point.

### Step 2 — Shared Federation Chats

Once two groups are aware of each other, they enter a provisional relationship and gain access to **federation chats** — special input/output chat pairs for evaluating the inter-group relationship. These chats discuss:

- First contact and introductions
- Evaluation of each group's principles
- Proposed settings for any shared chats they might create
- Whether to proceed to full federation

### Step 3 — Group Vouching

Federation uses a modified version of the member vouching mechanism:

1. Both groups must independently reach **internal consensus** to vouch for the other
2. Each group evaluates the other's principles and decides whether to endorse them
3. Vouching uses QR codes or security codes (mirroring individual onboarding)
4. Once both groups have vouched for each other and the threshold is met (default: **2 vouches** from each group), the relationship becomes **federated**

### Step 4 — Shared Chat Creation

Once federated:
- Either group can propose the creation of a **shared chat**
- The shared chat proposal includes its settings (visibility, consensus type, etc.)
- Both groups must consent to the settings before the chat is created
- The same [consent check mechanics](core-flow) govern shared chat discussions

---

## Shared Chats

A shared chat is a chat space accessible to members of both (or potentially more) federated groups.

- Each shared chat has **input/output** views, identical to within-group chats
- Both groups can see the same input chat and output chat
- Proposals in shared chats are scored using the same algorithm as single-group chats
- Chat settings must be consented to by members of both groups (bilateral consensus)

---

## Flat Hierarchy Principle

ConsensusUX Federation is not a higher tier of governance. It is the same layer extended outward:

| Structure | Direction | Mechanism |
|-----------|-----------|-----------|
| Subgroups | Downward (within a group) | A group creates a focused subspace |
| Federation | Upward (between groups) | Two groups form a lateral relationship |

Both use identical architecture. There are no federation administrators, no inter-group admin roles, and no settings that can be imposed on one group by another.

---

## Technical Spec

### Data Model

```
FederationRelationship
  group_a_id              FK → Group
  group_b_id              FK → Group
  status                  enum(connected | federated | de_federated)
  created_at              timestamp

GroupVouch
  vouching_group_id       FK → Group
  target_group_id         FK → Group
  vouch_count             integer
  required_vouches        integer   (default: 2)

SharedChat
  id                      uuid
  federation_relationship_id  FK → FederationRelationship
  chat_settings           FK → ChatSettings
  created_by_group_id     FK → Group

FederationChat
  id                      uuid
  federation_relationship_id  FK → FederationRelationship
  type                    enum(relations_eval | settings_proposal)
```

### Key Behaviors

- Both groups must **independently reach internal consensus** before their `GroupVouch` is registered — the decision to federate is not made by an individual member
- Shared chat settings require **bilateral consensus**: members of both groups participate in the consent check for any shared chat settings proposal
- **De-federation** can be initiated by either group (following internal consensus). It does not require the other group's consent
- Shared chat **history is preserved** after de-federation (read-only access for members of both former-federation groups)
- The `FederationRelationship` record persists after de-federation; status changes to `de_federated` rather than being deleted

---

## Open Questions

- **Group discovery**: How do groups find each other in the first place? A public directory creates a surveillance surface; a referral-only model may be too restrictive for new groups.
- **Multi-group shared chats**: Can more than two groups share a single chat? The data model uses a pairwise `FederationRelationship` — a multi-group shared chat would require a different structure.
- **Shared chat membership**: Are all members of both groups automatically members of every shared chat, or is a delegate/representative model needed for large groups?
- **Proposals during de-federation**: If a shared chat has active proposals (pending consent checks) at the moment de-federation is initiated, what happens to them?
