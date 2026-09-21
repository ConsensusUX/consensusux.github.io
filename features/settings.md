---
title: Settings & Configuration
parent: Features
nav_order: 6
layout: default
---

# Settings & Configuration

> This feature is planned. No production code has been written.
{: .planned }

A defining feature of ConsensusUX is that settings are not controlled by admins — they are **outputs of group consensus**. Every configurable aspect of the app is itself subject to the consent check process. A member proposes a settings change; the group deliberates; if consensus is reached, the setting updates. Settings that affect shared experience are resolved through [Relay Chats](relay-chat) — standalone consensus UIs with no paired input chat.

---

## Overview

Settings exist at three scopes:

| Scope | Governed by | Examples |
|-------|-------------|---------|
| Per-chat | Consensus of chat participants | Visibility, display options, checklist mode |
| Per-group | Group consensus | Vouch thresholds, federation, chat ordering |
| Per-user | Individual (non-consensus) | Personal notification preferences |

The distinction between per-user and group/chat settings is important: only personal preferences that have no effect on other members are controlled individually. Everything that affects the group experience requires group consensus.

---

## Safe Defaults Principle

When no group consensus exists on a setting — either because the setting has never been proposed, or because a proposal failed — the system applies the **most conservative** available option. Conservative means **least information exposure**.

> Safe defaults are hard-coded fallbacks, not consensus outcomes. They cannot be changed by a group consensus proposal. They represent the minimum viable trust posture for a group that has not yet discussed a given setting.
{: .note }

Examples of safe defaults:

- Visibility to prospective members: **off**
- Visibility to federated groups: **off**
- Visibility to external websites: **off**
- Message preview in notifications: **always off** (hard security constraint, not just a default)

---

## Per-Chat Settings

These settings apply to a single chat and are changed by consensus of that chat's participants. They are resolved via [Relay Chat](relay-chat).

### Chat Identity

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Name of chat | String input | Last consented to name with longest run | Resolved via relay chat |
| Chat Icon | Image | First letters of chat name | Resolved via relay chat |
| Can users retract messages | Y/N | Yes | Always on for profile pages; cannot be turned off there |

### Input Formatting

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Display users linkable name/profile image in input chat | Y/N | No | |
| Publish to additional federated Output chat | Multi choice Y/N | No | Choose from list of Output chats of federated groups visible to this group. Establishes a publishing link for all consented options. |

### Output Formatting

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Display users linkable name/profile image in Output chat | Y/N | No | |
| Only show green proposals | Y/N | No | Hides the red section entirely |
| Output formatted as a checklist | Y/N | No | Adds a checkbox beside each proposal. Sub-setting: **Resettable buttons on checklist** (Y/N, default No). See [Relay Chat — Checklist Output Chat](relay-chat#checklist-output-chat). |
| Output visible to prospective members | Y/N | No | Sub-setting: **Set prompt text** (string, default "I consent to be bound by the rules/values/principles of the group"). Sub-setting: **Set continue button text** (string, default "Submit"). |
| Output visible to external website | Y/N + String URL + API key | No | Requires URL and API key to enable |
| Output visible to federated groups | Multi choice Y/N | Initial setting at chat creation | Choose from federated groups. Non-exclusive. |
| Accept publishing from federated input chat | Multi choice Y/N | No | Choose from input chats already set to publish to this output. Note: publishing groups automatically get visibility. A group can have visibility without publishing, but not publishing without visibility. |
| Reset proposal consent when published from federated chat | Y/N | No | If Yes, proposals from other groups land in the yellow zone with no score. If No, consent status carries over from the originating group. |

> The majority consensus types (90% supermajority, 75% majority) have **gaming risk**: a sufficiently large faction can push through proposals that a smaller group strongly objects to, undermining the consent-based model. These options exist for groups that choose them deliberately, but they are not the recommended default.
{: .warning }

---

## Per-Group Settings

These settings apply to the group as a whole and are changed by group consensus.

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Name of group | String input | Last consented to name | |
| Icon of group | Image | First letters of name | |
| X vouches for user to be in group | Positive integer | Last consented to number | Number of positive vouches required to join |
| Y vouches to be federated | Positive integer | Last consented to number | Number of vouches required from each group to establish federation |
| Ordering of chats | Named scheme + ordered list | Most visibility options to least, alphabetical within | See [Relay Chat — Chat Ordering Schemes](relay-chat#chat-ordering-schemes). Sequential ordering; highest-consenting scheme is the group default. |
| Time based restriction between vouches for new members | Y/N + positive integer (days) | 14 days | Minimum time between successive vouch rounds for a new member candidate |

---

## Per-User Settings

These settings are controlled individually and are not subject to group consensus. They have no effect on other members' experience.

| Setting | Type | Default | Notes |
|---------|------|---------|-------|
| Push notifications (global) | Boolean | true | Master switch for all push notifications |
| Notification per chat | Boolean | true | Customise which chats send push notifications |
| Message preview in notifications | Boolean | false | **Always false — hard security constraint, not overridable** |

> Message preview in push notifications is a **hard security constraint**, not a configurable setting. It is not overridable by group consensus or individual preference. A notification that reveals message content exposes sensitive group communications to anyone who can see the user's lock screen.
{: .warning }

---

## Settings as Consensus

Any member can propose a settings change. Because settings changes affect the shared group experience, they are resolved through a [Relay Chat](relay-chat) — not through the standard input/output chat pair.

1. Member taps the relevant setting to open its relay chat.
2. Member taps **+** to propose a new value, optionally attaching reasoning.
3. The proposal enters the **yellow zone** with no score.
4. Other members navigate to the proposal, read the reasoning, and cast votes.
5. If the proposal reaches the consent threshold it rises to **green** and becomes the active value.
6. If it fails to reach threshold it sinks to **red**.

### Settings Change History

Every settings change is traceable to the proposal that authorized it. The relay chat preserves a full history of what the setting was, when it changed, and which consensus process changed it. For per-chat and per-group settings the `modified_by_proposal_id` foreign key links back through the relay chat entry to the judgment records that produced the outcome.

---

## Wireframes

![Settings / principles configuration](../assets/images/settings-screen.png)

---

## Technical Spec

### Data Model

```
ChatSettings
  chat_id                 FK → Chat (unique)
  settings_json           jsonb
  last_modified           timestamp
  modified_by_proposal_id FK → RelayChatEntry

GroupSettings
  group_id                FK → Group (unique)
  settings_json           jsonb
  last_modified           timestamp
  modified_by_proposal_id FK → RelayChatEntry

UserSettings
  user_id                 FK → User (unique)
  settings_json           jsonb   (personal preferences only; no consensus settings)

SettingsRelay
  id                      uuid
  scope                   enum(chat | group)
  target_id               uuid          (chat_id or group_id depending on scope)
  setting_key             string        (e.g. "name_of_chat", "ordering_scheme")
  relay_chat_id           FK → RelayChat
```

### Key Behaviours

- Settings changes are **proposals** processed through the relay chat consent flow — there is no separate admin settings panel.
- **Safe defaults** are hard-coded in application logic, not stored in `ChatSettings` or `GroupSettings`. A missing key in `settings_json` resolves to the safe default, not null.
- **Message preview** in notifications is enforced at the notification dispatch layer, not the settings layer. Even if a `UserSettings` record somehow contained a `message_preview: true` entry, the dispatch layer ignores it.
- **Settings history** is auditable through the `modified_by_proposal_id` foreign key, which links back to the relay chat entry and its associated judgment records.
- Federated visibility and publishing links are stored as arrays within `settings_json`; each entry in the multi-choice fields corresponds to one consented option in the relevant relay chat.

---

## Open Questions

- **Constitutional settings**: Should some settings require a supermajority to change, even if the group's current consensus type is standard? For example: changing the consensus type itself.
- **Safe defaults by consensus**: Can a group use a successful consensus proposal to permanently override a safe default (making a more permissive option the group's baseline)? Or are safe defaults truly immutable fallbacks?
- **Federation setting conflicts**: If two federated groups have conflicting settings for a shared chat — one group consented to public visibility, the other did not — which setting takes precedence? The more conservative? The more recent?
