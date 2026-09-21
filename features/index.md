---
title: Features
layout: default
nav_order: 2
has_children: true
---

# Features

This is the master feature inventory for ConsensusUX. Each feature page documents the design rationale, user flow, technical specification, and open questions for a distinct system capability.

ConsensusUX is designed for horizontal groups — communities, collectives, and organizations that make decisions together without hierarchical authority. The features below reflect that structure: no admins, no unilateral control, and settings that are themselves outputs of group consensus.

> Pages are updated as design and implementation progresses. Track development on [GitHub](https://github.com/ConsensusUX/ConsensusUX).
{: .note }

---

## Master Feature Inventory

| # | Feature | Status | Description | Priority |
|---|---------|--------|-------------|----------|
| 1 | [Getting on the Network](getting-on-network) | [M1](https://github.com/ConsensusUX/ConsensusUX/milestone/2) | Account creation, group discovery, QR scanning, vouching | P1 |
| 2 | [Group Membership](group-membership) | [M3](https://github.com/ConsensusUX/ConsensusUX/milestone/4) | Member list, profiles, vouch tracking, probationary status | P2 |
| 3 | [Core Flow](core-flow) | [M2](https://github.com/ConsensusUX/ConsensusUX/milestone/3) | Consent check, input/output chats, red/green scoring, chain messages | P3 |
| 4 | [Soft Lock Disputes](soft-lock-disputes) | [M4](https://github.com/ConsensusUX/ConsensusUX/milestone/5) | Conflict resolution, mutual lock, concession mechanics | P4 |
| 5 | [Federation](federation) | [M6](https://github.com/ConsensusUX/ConsensusUX/milestone/7) | Multi-group relations, shared chats, group vouching | P5 |
| 6 | [Settings & Configuration](settings) | [M5](https://github.com/ConsensusUX/ConsensusUX/milestone/6) | Per-chat, per-group, per-user settings via consensus | P6 |
| 7 | [Relay Chat](relay-chat) | [M5](https://github.com/ConsensusUX/ConsensusUX/milestone/6) | Standalone output chat for settings resolution, cross-group relaying, checklist formatting | P6 |

> Track development progress on the [GitHub milestones](https://github.com/ConsensusUX/ConsensusUX/milestones).
{: .note }

---

## Reading the Feature Pages

Each feature page includes:

- **Overview** — What the feature does and why it exists
- **User Flow** or **How It Works** — Step-by-step interaction walkthrough
- **UX Rationale** — Design decisions and their reasoning
- **Technical Spec** — Data model sketches and key implementation notes
- **Open Questions** — Unresolved design decisions

Feature pages are written for three audiences:

- **End users** — The overview and flow sections explain what they will experience
- **UX designers** — The rationale and Figma embeds support design work
- **Developers** — The technical spec and data models support implementation planning
