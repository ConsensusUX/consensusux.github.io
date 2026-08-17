# Figma Design Annotations

Raw annotation text extracted from the Figma file `Consensus_UX` (file key: `6xXQKCxXbBoOBSEGL9YzyX`).
These are designer notes attached to wireframe screens. They contain design decisions, open questions, and implementation notes that supplement the dissertation.

---

## Onboarding & Network

- Username is separate from any used name in any particular group
- Once "X" scans left == 0, screen moves on to groups page
- When scanned or security code inputted by member of group
- Do we specify that introduction of a new member requires that one full member vouch for them?

## Core Flow & Scoring

- When ordering, proposals are split into green/red by whether they have universal consent or not. The highest green score go to the top of the page, where they are most visible. The highest red scores go to the bottom, ordered upwards from highest to lowest.
- Should devolve options to have also access to 90% consent = green and maybe 75% consent = green for different situations. Occupy had to adopt the 90%. Which was a feasible compromise for the situation of an open street movement.
- All decisions prompt a drop down menu as to "why" when decision made.
- When we think about rules, it might be necessary that after a certain amount of time they expire, or at least weaken. New arrangements then need to be able to counteract old. A rate of decay consistent with the rate of admission of new members to the chat. Who has historically consent[ed].

## Settings & Configuration

- Setting Options: (1.) binary (y/n), (2.) insert text, (3.) non exclusive listed choices
- Need to game out how chat settings and scalping work via consensus. If we can't figure out a "good enough" one size fits all solution then we need to identify which features need to be devolved.
- In the "create new chat" screen are there any features that are missing for a minimal viable product?
- Need a complete list of set features to specify a group before we can do the options for a new group.
- Among the settings there needs to be a specification of channel ID for both input and output chats so they can be referenced as the output for many input chats as to allow collation of will between groups. Only output visible to other groups. Input chats are always between established groups.

## Membership & Scalping

- With profiles, hostile networks of new joiners should not be able to do a hostile take over of the network. This means we need provisional members who are easier to get rid of.
- In the case of scalping, a provisional member may be kicked off as soon as their red_score hits 1, or green_score falls below a certain amount of vouches. A full member soft locks the red voting party when consent is lost. They may not get unlocked unless they concede the point, someone else votes.
- Removed from Group: less than required positive vouches / No positive vouches aside from self vouch / super majority (3/4) of vouches are negative / No self vouch.
- Permissions for a member: add people to group, settings privileges.

## Soft Lock Disputes

- Soft locking is a system to push towards resolutions in cases of settings disputes and scalping, without resorting to some kind of majoritarian vote counting.
- Neither user can leave this page until the dispute is resolved.
- Resolution A: Michael concedes. This resolves with Michael leaving the group.
- Resolution B: Dissenter concedes. This resolves with the initial issue being addressed sufficiently to the dissenter's satisfaction.
- Instigates 1 week dispute period, notifications sent out.
- Stand aside button?

## Touch Points (UI Design Notes)

- Touch Points: A touch point is like the dashboard of a car. They indicate how a person steers, by foregrounding pertinent information that affords agency. An arrangement of static and dynamic displays and motions that extends the person's nervous system in real space.
- Touch Points: giving the ability to prioritize new information flow is a key point of the interface architecture.
- Red dot to indicate disputes or new proposals. When a new message is seen, the red dot on its nested grouping goes away.
- R = red_score, G = green_score — shows scoring that determines position in consent spectrum.

## Federation

- How does federation work with the underlying software we are using? Ideally we would like to facilitate information flows between groups.
- Federated Groups / Provisional Members / External Website visibility levels.

## Open Questions (from Figma)

- Explainer videos needed: (1.) core conception of consensus and how the core flow internalizes that — chain messages, ordering and institutionalization of decisions.
- Basic binary choice for notifications.
- Communication flows (needs documentation).
- Profile settings: amount of vouches required.
- Grouping of chats under specific headings.
- Purpose of chat (needs clarification per chat).
