# Week 2: Hexagonal Architecture Training - Foundation & CLI Tool

## Objectives

Build a **Ticket Manager CLI** applying Hexagonal Architecture and controlled AI usage during development.

**Focus Areas:**
- Understand and apply Hexagonal Architecture (Ports & Adapters pattern)
- Use AI with control through proper guardrails and validation (apply workflows from Week 1)
- Build maintainable and testable code with clear boundaries

## Training Materials

- Hexagonal Architecture Slides: [`slides-hexagon.md`](../../../slides-hexagon.md) - Ports & Adapters Pattern
- AI Training Slides: [`slides-ai-training.md`](../../../slides-ai-training.md) - Controlled AI Usage (from Week 1)

## Problem Statement

Build a CLI tool to manage tickets stored locally in JSON files. The tool should support:
- Creating tickets with title, description, status, priority, tags
- Listing tickets with filters (by status, priority, tags)
- Showing ticket details
- Updating ticket status

### Week 2 Focus: Foundation & Architecture Training

This week focuses on building a Ticket Manager CLI applying Hexagonal Architecture. Candidates will design domain models (Ticket entity, TicketService), implement core domain logic, set up Ports & Adapters, and build basic CLI commands (`tickets create`, `tickets list`, `tickets show`, `tickets update`) while applying AI workflows from Week 1.

## Acceptance Criteria

- [ ] Hexagonal Architecture is applied correctly: Domain independent, Ports defined, Adapters implement Ports
- [ ] CLI tool basic functions are working: `tickets create`, `tickets list`, `tickets show <id>`, `tickets update <id>`
- [ ] Core domain is testable without real adapters (using mocks)
- [ ] Unit tests for domain logic are written
- [ ] Setup instructions are documented: Installation, configuration, usage
- [ ] Questions about Hexagonal Architecture can be answered based on research and implementation
