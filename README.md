# Jmix Common Project (AI Reference)

A **Fullstack Jmix 2.7.x Application** (Java 17, Spring Boot, Vaadin Flow) serving as a **Reference Implementation for AI-Assisted Development**.

Copy AI configuration files from this repository into your own Jmix projects.

## Purpose

- Demonstrate how to configure AI coding assistants (Cursor, Claude, Copilot) for Jmix
- Provide standardized rules and skills for Jmix entities, views, and security
- Ensure cross-vendor compatibility for AI tools

## Quick Start (Jmix App)

```bash
./gradlew bootRun     # http://localhost:8080 (admin/admin)
./gradlew test        # Run tests
./gradlew build       # Build project
```

## AI Configuration Files

| File | Tool | Purpose |
|------|------|---------|
| [`.cursorrules`](.cursorrules) | Cursor IDE | Main rules and forbidden patterns |
| [`CLAUDE.md`](CLAUDE.md) | Claude Code | Context for Anthropic CLI |
| [`AGENTS.md`](AGENTS.md) | OpenAI Codex | Instructions for autonomous agents |
| [`AI_CONTEXT.md`](AI_CONTEXT.md) | Universal | Fallback context for any AI tool |
| [`.github/copilot-instructions.md`](.github/copilot-instructions.md) | GitHub Copilot | VS Code Copilot instructions |

## Skills (Detailed Guides)

Located in [`docs/skills/`](docs/skills/):

| File | Topic |
|------|-------|
| `00-jmix-overview.md` | What is Jmix (vs Spring Boot) |
| `01-entities-skill.md` | How to create entities |
| `02-views-skill.md` | How to create UI views |
| `03-services-skill.md` | Business logic & DataManager |
| `04-security-skill.md` | Roles & policies |
| `05-testing-skill.md` | Unit & UI testing |
| `06-liquibase-skill.md` | Database migrations |

**Note:** Cursor IDE uses symlink `.cursor/skills` → `docs/skills` for auto-discovery.

## Project Structure

```
jmix-common-project/
├── src/main/java/.../entity/      # Jmix Entities
├── src/main/java/.../view/        # Vaadin UI Controllers
├── src/main/resources/.../view/   # XML Descriptors
├── src/main/java/.../security/    # Resource Roles
├── src/main/resources/liquibase/  # DB Migrations
└── docs/skills/                   # AI Skills (vendor-neutral)
```

## MCP

If `jmix-rag-mcp-search` is configured:
- AI assistants will use it for Jmix-specific queries
- Replaces general web search for framework questions
