---
title: Scoring Algorithm
parent: Development
nav_order: 2
---

# Scoring Algorithm

## Overview

The scoring algorithm determines how proposals are ordered in the output chat. It converts individual judgments — binary (up/down) or degrees of resistance (1–10) — into `green_score` and `red_score` values that control a proposal's visibility and position.

---

## Input: Judgments

Each judgment on a proposal carries:

- **Type**: `binary` (thumbs up/down) or `degrees` (1–10 scale of resistance)
- **Value**: the specific judgment value
- **Reasoning**: required text explaining the judgment — reasoning is always mandatory

---

## Score Mapping

| Judgment Value | Score Type | Contribution |
|---|---|---|
| Binary: up (1) | `green_score` | Positive contribution |
| Binary: down (0) | `red_score` | Positive contribution |
| Degrees: 1–2 | `green_score` | Strong positive |
| Degrees: 3 | `green_score` | Moderate positive |
| Degree: 4 | `green_score` | Weak positive |
| Degree: 5 | `green_score` | Midpoint (`green_score → 0.5`) |
| Degree: 6 | `red_score` | Midpoint (`red_score → 0.5`) |
| Degree: 7 | `red_score` | Moderate positive |
| Degrees: 8 | `red_score` | Strong positive |
| Degrees: 9–10 | `red_score` | Hard dissent |

---

## Worked Examples

The following examples are drawn from the dissertation.

**Example 1:** 3 judgments, all thumbs up
- `green_score` = thumbs_up_count / total_judgments = 3/3 → `green_score` approaches **1.0**
- `red_score` = 0

**Example 2:** 8 judgments, all thumbs down
- `red_score` = thumbs_down_count / total_judgments = 8/8 → `red_score` approaches **1.0**
- `green_score` = 0

**Example 3:** Binary — 2 up, 1 down
- `green_score` = 2/3 ≈ **0.67**
- `red_score` = 1/3 ≈ **0.33**

**Example 4:** Degrees judgments of 9 and 10 (hard dissent)
- `red_score` = **1.0**

---

## Output Chat Ordering Rules

```
if (proposal.red_score >= 1.0):
    // Place in RED SECTION (lower visibility)
    // Order within red section: higher red_score = less visible

else:
    // Place in GREEN SECTION
    // Order: higher green_score = more visible (top position)
```

---

## Chain Score Propagation

Chain messages dynamically affect the parent proposal's score through a **cancellation mechanic**:

- If Person 1 adds a consenting judgment (`green_score +1`) and Person 2 challenges it with a dissenting chain message, and Person 1 then **sustains** Person 2's challenge (green vote on the dissent), Person 1's original `green_score` contribution is cancelled out.
- The same applies in reverse: if a `red_score` contribution is challenged and the original dissenter sustains the challenge, the `red_score` is cancelled.
- This means output chat rankings are **dynamic** — proposals can recover from misunderstood objections through chain deliberation.

The cancellation mechanic provides the scaffolding for addressing bad faith actors who have gained group access.

---

## Visualization

The output chat presents proposals in two sections:

- **Green section**: high-consent proposals, visible to all (including prospective members if chat settings permit)
- **Red section**: low-consent proposals, hidden from prospective members by default
- **X/Y badge**: X = number of direct judgments on the proposal, Y = depth of the longest judgment chain

---

## Implementation Notes

```typescript
function calculateProposalScore(judgments: Judgment[]): ProposalScore {
  let green_score = 0;
  let red_score = 0;

  for (const j of judgments) {
    if (j.type === 'binary') {
      if (j.value === 1) green_score += 1;
      else red_score += 1;
    } else {
      // degrees: 1-10
      if (j.value <= 5) green_score += (6 - j.value) / 5; // 1→1.0, 2→0.8, 3→0.6, 4→0.4, 5→0.2
      else red_score += (j.value - 5) / 5;                // 6→0.2, 7→0.4, 8→0.6, 9→0.8, 10→1.0
    }
  }

  const total = judgments.length || 1;
  return {
    green_score: green_score / total,
    red_score: red_score / total,
    judgment_count: judgments.length,
    chain_depth: calculateMaxChainDepth(judgments),
    last_updated: new Date()
  };
}
```

> The scoring formula above is a proposed implementation based on examples in the dissertation. The exact formula needs validation through community testing and game-theoretic analysis to ensure it cannot be easily gamed.
{: .warning }

---

## Open Questions

- Should score contributions be weighted by chain depth?
- How to handle abstentions? (judgment count affects the denominator)
- Should there be a minimum judgment count before ordering takes effect?
- Time decay: should older judgments matter less?
- How to prevent coordinated gaming of scores?
