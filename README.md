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
| [`.continuerules`](.continuerules) | Continue.dev | VS Code / JetBrains extension |

## Skills (Detailed Guides)

Located in [`.cursor/skills/`](.cursor/skills/) — each skill is a folder with `SKILL.md`:

| Folder | Topic |
|--------|-------|
| `jmix-overview/` | What is Jmix (vs Spring Boot) |
| `entities/` | How to create entities |
| `views/` | How to create UI views |
| `services/` | Business logic & DataManager |
| `security/` | Roles & policies |
| `testing/` | Unit & UI testing |
| `liquibase/` | Database migrations |
| `dto/` | Data Transfer Objects |
| `enums/` | Enumerations with EnumClass |
| `fetchplans/` | Lazy loading & fetch plans |


## Rules (Context-Specific)

Located in [`.cursor/rules/`](.cursor/rules/) — each rule is a folder with `RULE.md`:

| Folder | Scope |
|--------|-------|
| `jmix-overview/` | Always applies |
| `entities/` | `**/entity/**` |
| `views/` | `**/view/**` |
| `services/` | `**/service/**` |
| `security/` | `**/security/**` |
| `testing/` | `**/test/**` |
| `liquibase/` | `**/liquibase/**` |
| `dto/` | `**/dto/**` |
| `enums/` | Enum files |
| `fetchplans/` | Data loading |

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
