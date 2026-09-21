---
title: Relay Chat
parent: Features
nav_order: 7
layout: default
---

# Relay Chat

> This feature is planned. No production code has been written.
{: .planned }

A relay chat is a specialised output chat used where there is no traditional input chat — or where proposals are relayed between input chats. It is the primary UI through which settings are configured by consensus.

---

## Overview

Most chats in ConsensusUX are input/output pairs: members post to an input chat, and proposals surface in the corresponding output chat. A relay chat breaks this pattern. It is **standalone** — there is no paired input chat. Instead, members contribute directly into the relay chat itself, into a yellow "pending" zone, using a **+** button.

Relay chats appear wherever a group needs to reach consensus on something that has no natural input chat home: group settings, chat settings, ordering schemes, and similar configuration decisions.

---

## Visual Structure

A relay chat uses the same three-zone layout as a standard output chat, with one key difference: the yellow zone is always visible and interactive.

| Zone | Colour | Meaning |
|------|--------|---------|
| Top | 🟢 Green | Consented — the active value |
| Middle | 🟡 Yellow | Pending — proposed but no consensus yet |
| Bottom | 🔴 Red | Rejected — did not reach consensus |

The yellow zone is what distinguishes a relay chat from a standard output chat. In a standard output chat the yellow zone is a transient holding area; in a relay chat it is the primary entry point for new proposals. Members tap **+** to add a new entry directly into yellow.

---

## Exclusive vs Non-Exclusive Settings

Settings in relay chats fall into one of two categories, indicated by star markers:

**★ Exclusive settings** — only one option can be active at a time. The top-consented green entry is the active value. There is always a **backstop default**: a hard-coded fallback that becomes active if nothing has reached green. This ensures the setting is never undefined.

**★★ Non-exclusive settings** — multiple options can be active simultaneously. All green entries are active at once. Because multiple values are valid together, no backstop is needed.

> Exclusive settings always have a backstop default hard-coded in application logic. The backstop is the safe-default value and cannot be displaced by a proposal — it is only superseded when a proposal rises to green.
{: .note }

---

## How Settings Are Changed

The following example walks through changing a chat's name using a relay chat.

1. A member taps the chat's settings icon and selects **Name of chat**.
2. The relay chat for that setting opens, showing the current name in the green zone.
3. The member taps **+** to propose a new name, entering the text in a string input field.
4. The proposal appears in the **yellow zone** with no score.
5. Other members navigate to the proposal, read any reasoning attached, and cast votes.
6. As votes accumulate the proposal's score rises or falls. If it surpasses the current green entry's score and meets the consent threshold, it rises to the **green zone** and becomes the active name.
7. If it falls below threshold it sinks to the **red zone**.

At every point the current active value remains the top green entry. The relay chat is a living record of what the group has tried, rejected, and consented to.

---

## Chat Ordering Schemes

Groups can create **named ordering schemes** to control how chats are listed in the sidebar. Each scheme is a relay-chat entry: a named configuration containing an ordered list of chats.

Example scheme names a group might propose:

- *New Member Scheme* — surfaces onboarding and rules chats at the top
- *The manic designer scheme* — prioritises creative and asset chats
- *The no bullshit dev scheme* — strips everything except code review and standup
- *Events and Workshops* — promotes event planning chats during active periods

**How ordering works:**

- The top-consented scheme is the **group default** shown to all members on first load.
- Individual members can **switch personally** to any scheme the group has consented to, overriding the default for themselves only.
- Within a scheme, chats can use **decimal ordering** (e.g. 1.0, 1.1, 2.0) to allow fine-grained insertion without renumbering.
- **Heading rows** can be inserted between chats to create labelled sections within a scheme (e.g. a "─── Admin ───" heading above the settings and membership chats).

Because ordering schemes are themselves relay-chat entries, they follow the same rise/fall consensus logic as any other setting: propose, deliberate, consent.

---

## Checklist Output Chat

Any output chat can be formatted as a checklist by enabling the **Output a checklist** setting (resolved via relay chat).

When enabled:

- Each proposal in the output chat gains a **checkbox** beside it.
- Any member can tick or untick a checkbox independently of the consent process.
- Combined with the **Display users linkable name/profile image in Output chat** setting, the chat shows **who last ticked or unticked each item and when**.

A sub-setting — **Resettable buttons on checklist** (Y/N, default No) — controls whether checkboxes can be reset to unticked after being ticked, or are one-way.

**Example use cases:**

- *Community meals* — a shopping list where members tick items as they are purchased
- *Cleaning rotas* — recurring tasks members mark done each week; reset at the start of the next cycle
- *Repeatable procedures* — onboarding steps, safety checks, event setup lists

> Checklist mode does not affect the consent process. Ticking a checkbox is not a vote. Proposals still rise and fall through the normal judgment flow; the checkbox is a separate layer of task-tracking on top of consented content.
{: .note }

---

## Technical Spec

### Data Model

```
RelayChat
  id                      uuid
  chat_id                 FK → Chat (nullable — null if standalone relay)
  setting_key             string          (e.g. "name_of_chat", "ordering_scheme")
  is_exclusive            boolean         (★ vs ★★)
  backstop_default        jsonb           (only populated when is_exclusive = true)
  created_at              timestamp

RelayChatEntry
  id                      uuid
  relay_chat_id           FK → RelayChat
  proposed_by             FK → User
  value                   jsonb           (the proposed value: string, image ref, ordered list, etc.)
  reasoning               text            (optional rationale attached at proposal time)
  status                  enum(pending | green | red)
  consent_score           integer
  checkbox_ticked_by      FK → User       (nullable — only populated in checklist mode)
  checkbox_ticked_at      timestamp       (nullable)
  created_at              timestamp
  resolved_at             timestamp       (nullable — when status left pending)
```

### Key Behaviours

- A **RelayChat** is created automatically when a setting that uses relay-chat resolution is first accessed. The backstop default is baked into the record at creation time and is immutable.
- **RelayChatEntry** rows are created when a member taps **+**. They enter with `status = pending` and `consent_score = 0`.
- Score updates trigger a re-sort. For exclusive settings, exactly one entry with the highest score above threshold is promoted to `green`; all others at or below threshold remain `pending` or are demoted to `red`.
- For non-exclusive settings, **all** entries above threshold are `green` simultaneously.
- Checklist ticks update `checkbox_ticked_by` and `checkbox_ticked_at` in place — they are not separate events.

---

## Open Questions

- **Rollback grace period**: If a setting rises to green and then immediately falls back to yellow (rapid reversal of opinion), should there be a grace period before the previous green value is reinstated, to avoid flickering?
- **Tie-breaking**: For exclusive settings, if two entries share the highest consent score, which is active? Last-to-reach-that-score? A secondary sort by age?
- **Relay chat visibility**: Who can see a relay chat for a given setting? All members of the chat/group to which the setting belongs? Should prospective members ever see settings relay chats?
