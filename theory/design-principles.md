---
title: Design Principles
parent: Theory
nav_order: 2
---

# Design Principles

These principles translate the theoretical commitments documented in [Academic Foundation](academic-foundation) into concrete implementation decisions. Every feature in ConsensusUX can be traced to one or more of these principles.

---

## Core Philosophy

### Medium is the Message

The platform's form is inseparable from its political content. Equitable and participative methods in design produce equitable tools — and equitable tools produce equitable outcomes. This is the prefiguration principle applied to software: you cannot build horizontal infrastructure through vertical processes.

This also means keeping production lines short. Community control requires that the communities using a tool can understand, modify, and if necessary replace it. Complexity that serves the developer at the expense of the user is a political choice, not a technical necessity.

### Horizontal Organization

No user in ConsensusUX has elevated permissions over other users. There are no admins. All settings — from group membership thresholds to communication defaults — are determined by group consensus. This is not a configuration option; it is a structural property of the platform.

### Disproportionate Advantage for Horizontal Groups

The UX is deliberately designed to make egalitarian, discursive organization easier and to make authoritarian operation harder. A group attempting to concentrate power, suppress dissent, or bypass consent mechanisms will find the platform actively resistant to those goals. This is a political commitment, not a neutral design position.

> Groups that organize horizontally should find ConsensusUX natural. Groups that depend on hierarchy should find it frustrating.
{: .note }

---

## The Nine Principles

### 1. No Admin Privileges

All settings are devolved to group consensus. No individual user holds elevated permissions over others. Moderation, configuration changes, and membership decisions all flow through the group's established consensus processes.

This directly prevents the platform from being captured by a single actor, whether an individual or an external organization. It also means that no one can be pressured or compelled to "just change the settings" — there are no settings that one person controls.

### 2. Security by Architecture

Security in ConsensusUX is structural, not policy-based. The platform does not ask users to trust that administrators will handle their data responsibly — it is architected so that certain categories of harm are technically impossible.

Key structural choices:
- No email registration (eliminates a major surveillance and deanonymization vector)
- Identity segmentation per group (prevents cross-group triangulation)
- No message preview caching (prevents metadata exposure)

Security by policy ("we promise not to misuse your data") fails when policies change, when administrators are compromised, or when legal compulsion applies. Security by architecture holds under all of those conditions.

### 3. Accessibility First

Participation is only meaningful if it is accessible. ConsensusUX builds accessibility in from the start rather than treating it as a compliance afterthought.

This includes:
- **Colorblind support** via texture and pattern in addition to color coding
- **Non-touchscreen alternatives** for all interactions, including security code input
- **Multiple information formats** so that complex information is presented in more than one way
- **Minimal jargon** so that the platform can be used by people who did not design it

Accessibility and security are treated as complementary rather than in tension. The security code system, for example, is designed to work without requiring a touchscreen.

### 4. Safe Defaults

When a group has not yet reached consensus on a setting, the platform applies the most conservative available option — the one that exposes the least information and grants the least access.

> Safe defaults mean that inaction protects rather than exposes. Groups must actively choose openness; they cannot accidentally drift into it.
{: .note }

This matters especially for new groups and for settings that members may not realize exist. A group that has not discussed its location-sharing policy, for example, does not share locations by default.

### 5. Identity Segmentation

A user's identity in one group is completely independent of their identity in any other group. Profiles are group-scoped: display names, vouching relationships, trust scores, and participation histories are not shared across groups.

This prevents **identity triangulation** — the process by which combining partial identity information from multiple contexts allows an observer to build a complete profile of a person. A user can participate in a housing rights group and a political organizing group without either group knowing about the other, and without an external observer being able to link the two identities.

### 6. Informed Consent

Consent in ConsensusUX is active and specific, not passive and general.

- Users view a group's stated principles before joining; they join knowing what they are agreeing to
- Vouchers must provide reasoning when vouching for a new member, not just approval
- Every significant judgment — trust score changes, dispute escalations, membership decisions — requires an explicit explanation from the actor making it

This creates an accountability trail and prevents consent from being manufactured through obscurity. Users always know what they are agreeing to and why decisions affecting them have been made.

### 7. Complexity Reduction

Governance is inherently complex. The platform's job is to make that complexity manageable without hiding it.

Techniques used:
- **Modular screen templates** that present consistent information architecture across different decision types
- **Intrinsic vs. extrinsic coloring** — color used to convey inherent properties (intrinsic) vs. relational context (extrinsic), keeping meaning consistent
- **Searchability** across members, proposals, and histories
- **Optional touchpoints** — features that are available but not required, reducing cognitive overhead for groups that do not need them
- **Progressive disclosure** — detailed information is available but not presented until requested

The goal is a platform that a new user can begin using immediately while offering the depth needed for sophisticated governance over time.

### 8. Personally Costly Action

Mechanisms for conflict and dispute are designed so that using them has a cost for the initiating party as well as the target. The soft lock dispute mechanism, for example, affects both parties symmetrically — the person raising a dispute and the person being disputed both experience the same constraints.

> If raising a dispute costs you nothing, disputes become cheap attacks. Personal cost is what makes dispute mechanisms serious rather than weaponizable.
{: .note }

This principle extends to all high-stakes actions in the platform. Actions that significantly affect others require investment — time, explanation, reputation — from the person taking them. This is not a punishment; it is a structural guarantee that the mechanism will be used for genuine concerns rather than strategic harassment.

### 9. Transparency of Process

No black boxes. Score calculations, membership decision chains, vouch histories, and setting changes are all auditable by the relevant group members.

This applies especially to algorithmic processes. Where other platforms use opaque ranking algorithms that users cannot inspect or contest, ConsensusUX shows its work. A member whose trust score changes can see exactly which events caused the change and how the calculation was performed. A group can audit its own decision-making history without requesting access from a platform operator.

Transparency of process is a prerequisite for meaningful consent. You cannot meaningfully agree to a process you cannot see.

---

## Anti-Patterns Explicitly Avoided

The following design patterns are common in civic tech and social software. ConsensusUX explicitly refuses them.

**Legal name requirements**
Requiring legal names excludes and endangers at-risk users — people fleeing domestic violence, people whose legal names expose their gender history, people whose political activity makes their legal identity a liability. Pseudonymity is not a loophole; it is a protection.

**Blockchain implementation**
Blockchain's irreversibility is presented as a feature but functions as a harm amplifier: mistakes, compromised keys, and coerced actions cannot be undone. The computational cost is real and the environmental impact significant, while the claimed benefits (immutability, trustlessness) can be achieved through architecture without a distributed ledger.

**Cynical or distrusting system design**
Excessive scripting of user behavior — systems that assume users will behave badly and design every interaction to prevent it — produce alienating, restrictive experiences that undermine genuine participation. ConsensusUX is designed for communities, not against them.

**Excessive technical jargon**
Jargon functions as a barrier that excludes non-specialists and concentrates interpretive power in those who already understand the system. Plain language is a political commitment.

**Centralized admin privileges**
Any single point of control is a point of capture. Admin privileges can be seized, compelled, or corrupted. The absence of admin privileges is not a missing feature; it is the feature.

**Email registration**
Email addresses are surveillance infrastructure. They link identities across services, enable phishing, and can be compelled from providers by legal process. ConsensusUX uses alternative authentication that does not require an email address.

**Black-box algorithmic ranking**
When the algorithm is opaque, power flows to whoever controls the algorithm. Transparent, auditable scoring — however imperfect — keeps power with the community rather than the platform operator.
