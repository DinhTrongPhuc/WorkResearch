# Week 3: Hexagonal Architecture Training - Extending with Odoo API and research MCP, Knowledge Base (vector)

## Objectives

Extend the CLI Build Tool from Week 2 to integrate with Odoo API for operations workflows.

**Focus Areas:**

- Add HTTP client adapter for external API integration
- Integrate Odoo API for ticket operations
- Extend CLI commands for Odoo ticket queries
- Apply AI workflows from Week 1 effectively

**Training Materials:**

- Hexagonal Architecture Slides: [`slides-hexagon.md`](../../../slides-hexagon.md) - Ports & Adapters Pattern
- AI Training Slides: [`slides-ai-training.md`](../../../slides-ai-training.md) - Controlled AI Usage

### Week 3 Focus: Odoo API Integration

This week extends the CLI tool from Week 2 to integrate with Odoo API. Candidates will implement HTTP client adapter, Odoo API client, ticket domain entities, and CLI commands (`tickets list`, `tickets new`, `tickets unprocessed`, `tickets show <id>`) to retrieve ticket data from Odoo.

## Acceptance Criteria

- [ ] Odoo API integration is working: Can authenticate and connect to Odoo
- [ ] Ticket data can be retrieved from Odoo: New tickets, unprocessed tickets, ticket details (time, tags, status)
- [ ] CLI commands for Odoo are working: `tickets list`, `tickets new`, `tickets unprocessed`, `tickets show <id>`
- [ ] Integration tests with mock Odoo API are written
- [ ] Setup instructions are documented: Odoo API configuration, authentication, environment setup
- [ ] Flow documentation is created: How CLI commands interact with Odoo API, data flow, error handling
