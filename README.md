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

Located in [`.cursor/skills/`](.cursor/skills/):

| File | Topic |
|------|-------|
| `00-jmix-overview.md` | What is Jmix (vs Spring Boot) |
| `01-entities-skill.md` | How to create entities |
| `02-views-skill.md` | How to create UI views |
| `03-services-skill.md` | Business logic & DataManager |
| `04-security-skill.md` | Roles & policies |
| `05-testing-skill.md` | Unit & UI testing |
| `06-liquibase-skill.md` | Database migrations |
| `07-dto-skill.md` | Data Transfer Objects |
| `08-enums-skill.md` | Enumerations with EnumClass |
| `09-fetchplans-skill.md` | Lazy loading & fetch plans |


## Rules (Context-Specific)

Located in [`.cursor/rules/`](.cursor/rules/):

| File | Scope |
|------|-------|
| `001-entities-rule.mdc` | `**/entity/**` |
| `002-views-rule.mdc` | `**/view/**` |
| `003-services-rule.mdc` | `**/service/**` |
| `004-security-rule.mdc` | `**/security/**` |
| `005-testing-rule.mdc` | `**/test/**` |
| `006-liquibase-rule.mdc` | `**/liquibase/**` |
| `007-dto-rule.mdc` | `**/dto/**` |
| `008-enums-rule.mdc` | Enum files |
| `009-fetchplans-rule.mdc` | Data loading |

## Project Structure

```
jmix-common-project/
├── src/main/java/.../entity/      # Jmix Entities
├── src/main/java/.../view/        # Vaadin UI Controllers
├── src/main/java/.../security/    # Resource Roles
├── src/main/java/.../service/     # Business Logic
├── src/main/resources/.../view/   # XML Descriptors
├── src/main/resources/liquibase/  # DB Migrations
├── .cursor/skills/                # AI Skills
└── .cursor/rules/                 # AI Rules (context-specific)
```

## RAG-based MCP (Optional)

If `jmix-rag-mcp-search` is configured:
- AI assistants will use it for Jmix-specific queries
- Replaces general web search for framework questions

If `idea-mcp` is configured:
- Use `get_file_problems(onlyErrors=false)` to check for warnings and errors
