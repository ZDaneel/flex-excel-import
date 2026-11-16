# ADR-0001: Record Architecture Decisions

- **Status:** Accepted
- **Date:** 2026-11-17

## Context

Architecture decisions tend to leak out of code review threads and chat logs, leaving future contributors (and future-me) to reconstruct the reasoning behind non-obvious choices. For a long-lived codebase, the decisions matter at least as much as the code they produced.

## Decision

Use **Architecture Decision Records** (ADRs) to capture significant decisions. One markdown file per decision in `docs/adr/`, numbered sequentially, using the lightweight template popularized by Michael Nygard.

Each ADR has four sections: **Context**, **Decision**, **Consequences**, and **Status**. Once an ADR is accepted, its file is immutable; superseding decisions reference it by number and set the old one to `Superseded by ADR-NNNN`.

## Consequences

**Positive**

- New contributors can read `docs/adr/` to understand *why* the codebase looks the way it does.
- Decisions are harder to silently reverse — an ADR change is visible in review.
- Interview-friendly: the ADR log doubles as a design-thinking artifact.

**Negative**

- Small overhead per decision. Mitigation: only record decisions with real optionality or trade-offs; skip trivia.

## References

- [Michael Nygard, "Documenting Architecture Decisions" (2011)](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- [adr.github.io](https://adr.github.io/)
