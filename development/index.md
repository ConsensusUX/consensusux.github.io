---
title: Development
nav_order: 5
has_children: true
---

# Development

This section contains technical documentation for developers building and contributing to ConsensusUX.

ConsensusUX is an **open source** project. All technical specifications documented here are derived from the UX dissertation and community co-design process — the same theoretical and participatory foundations that shape the broader project.

## Target Stack

- **Web**: React (single-page or server-side rendered — TBD)
- **Mobile**: React Native (iOS + Android)
- **Shared**: Core business logic, data models, and scoring algorithms in shared TypeScript packages, maximising code reuse across platforms
- **Backend**: TBD — real-time messaging requirements (live chat, notifications) suggest WebSocket or SSE support

The goal is a shared codebase wherever practical, so that the web and mobile experiences remain consistent and maintenance overhead is minimised.

## In This Section

- [Architecture](architecture) — System architecture, platform strategy, and core data models
- [Scoring Algorithm](scoring-algorithm) — How proposals are scored and ordered in the output chat
- [Contributing](contributing) — How to contribute code, design, research, or documentation
