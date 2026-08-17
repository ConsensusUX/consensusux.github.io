---
title: Architecture
parent: Development
nav_order: 1
---

# Architecture

## Overview

ConsensusUX is a multi-platform application enabling consensus-based decision-making for horizontal community groups. This page documents the proposed system architecture and data models.

---

## Platform Strategy

| Layer | Technology |
|---|---|
| **Web** | React (SPA or SSR — TBD) |
| **Mobile** | React Native (iOS + Android) |
| **Shared** | Core business logic, data models, and scoring algorithms in shared TypeScript packages |
| **Backend** | TBD — real-time messaging suggests WebSocket or SSE support |

Shared TypeScript packages allow core logic (scoring, data models, validation rules) to be written once and consumed by both the web and mobile surfaces.

---

## Core Data Models

```typescript
// === Users & Identity ===
interface User {
  id: string;
  private_username: string;
  password_hash: string;
  captcha_verified: boolean;
  created_at: Date;
}

interface GroupProfile {
  id: string;
  user_id: string;
  group_id: string;
  public_username: string;
  self_description: string;
  profile_photo_url: string | null;
  self_vouch_text: string;
  created_at: Date;
}

// === Groups & Membership ===
interface Group {
  id: string;
  name: string;
  principles_chat_id: string;
  settings: GroupSettings;
  created_at: Date;
}

type MemberTier = 'probationary' | 'full' | 'provisional' | 'removed';

interface GroupMember {
  user_id: string;
  group_id: string;
  tier: MemberTier;
  joined_at: Date;
  probation_ends_at: Date | null;
  removed_at: Date | null;
}

// === Vouching ===
interface VouchRequest {
  id: string;
  candidate_id: string;
  group_id: string;
  required_vouches: number; // default 4
  status: 'pending' | 'approved' | 'rejected';
  created_at: Date;
}

interface Vouch {
  id: string;
  voucher_id: string;
  candidate_id: string;
  group_id: string;
  judgment_type: 'binary' | 'degrees';
  judgment_value: number; // 0/1 for binary, 1-10 for degrees
  reasoning: string;
  created_at: Date;
}

// === Chats & Messages ===
type ChatType = 'input' | 'output';

interface Chat {
  id: string;
  group_id: string;
  type: ChatType;
  paired_chat_id: string; // input ↔ output pairing
  settings: ChatSettings;
  created_at: Date;
}

interface Message {
  id: string;
  chat_id: string;
  author_id: string;
  content: string;
  parent_message_id: string | null; // null for root messages
  created_at: Date;
}

// === Judgments & Scoring ===
type JudgmentType = 'binary' | 'degrees';

interface Judgment {
  id: string;
  message_id: string;
  judge_id: string;
  type: JudgmentType;
  value: number; // 0=down/1=up for binary; 1-10 for degrees
  reasoning: string;
  created_at: Date;
}

interface ProposalScore {
  message_id: string;
  green_score: number;
  red_score: number;
  judgment_count: number;
  chain_depth: number;
  last_updated: Date;
}

// === Disputes ===
type DisputeStatus =
  | 'active'
  | 'resolved_accused_concedes'
  | 'resolved_challenger_concedes'
  | 'cancelled';

interface Dispute {
  id: string;
  challenger_id: string;
  accused_id: string;
  group_id: string;
  complaint_message_id: string;
  status: DisputeStatus;
  created_at: Date;
  resolved_at: Date | null;
}

interface SoftLock {
  id: string;
  user_id: string;
  dispute_id: string;
  locked_at: Date;
  unlocked_at: Date | null;
}

// === Federation ===
type FederationStatus = 'connected' | 'federated' | 'de_federated';

interface FederationRelationship {
  id: string;
  group_a_id: string;
  group_b_id: string;
  status: FederationStatus;
  created_at: Date;
}

interface SharedChat {
  id: string;
  federation_relationship_id: string;
  settings: ChatSettings;
  created_at: Date;
}

// === Settings ===
interface ChatSettings {
  name: string;
  visible_to_prospective: boolean;   // default: false
  visible_to_federated: boolean;     // default: false
  visible_to_external: boolean;      // default: false
  consensus_type: 'standard' | 'supermajority_90' | 'majority_75';
  show_poster_name: boolean;         // default: true
  show_intrinsic_coloring: boolean;  // default: true
  show_scores: boolean;              // default: true
  show_timestamps: boolean;          // default: true
  show_chain_counter: boolean;       // default: true
}

interface GroupSettings {
  required_vouches: number;            // default: 4
  probation_months: number;            // default: 3
  vouch_refresh_days: number | null;
  vouch_time_restriction_days: number | null;
  required_federation_vouches: number; // default: 2
}

interface UserSettings {
  push_notifications: boolean;         // default: true
  per_chat_notifications: Record<string, boolean>;
  // message_preview: ALWAYS false — hard security constraint
}
```

---

## System Boundaries

> **Security Invariants (non-negotiable):**
> - No email anywhere in the system
> - Message preview in push notifications is always disabled
> - Profile data is group-scoped, never global
> - QR codes contain session tokens, not user identity
> - Safe defaults apply when no consensus exists
{: .note }

---

## Architecture Decisions TBD

The following decisions remain open and will be resolved during the implementation phase:

- **Database**: Relational (PostgreSQL) vs. document (MongoDB). The relational structure of groups, members, vouches, and disputes strongly suggests PostgreSQL.
- **Real-time**: WebSocket vs. SSE for live chat and notifications.
- **Offline-first**: Needed for at-risk users with intermittent connectivity?
- **End-to-end encryption**: Highly desirable for at-risk communities, but complicates server-side scoring and output chat generation.
- **Self-hosting**: Should groups be able to run their own instance of the server?
