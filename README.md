# Jmix Common Project (AI Reference)

This is a **Fullstack Jmix 2.7.x Application** (Java 17, Spring Boot, Vaadin Flow) that serves as a **Reference Implementation for AI-Assisted Development**.

Use this repository to copy-paste AI configuration files (`.cursorrules`, `CLAUDE.md`, etc.) into your own Jmix projects.

## 🎯 Purpose

- Demonstrate how to configure AI coding assistants (Cursor, Claude, Copilot) for Jmix.
- Provide standardized rules and skills for Jmix entities, views, and security.
- Ensure cross-vendor compatibility for AI tools.

## 🚀 Quick Start (Jmix App)

```bash
./gradlew bootRun     # App: http://localhost:8080 (admin/admin)
./gradlew test        # Run tests
./gradlew build       # Build project
```

## 🤖 AI Configuration Files (Copy to your project)

| File | Tool | Purpose |
|------|------|---------|
| [`.cursorrules`](.cursorrules) | Cursor IDE | Main rules, context, and forbidden patterns |
| [`CLAUDE.md`](CLAUDE.md) | Claude Code | Quick context for Anthropic's CLI tools |
| [`AGENTS.md`](AGENTS.md) | OpenAI/Codex | Instructions for autonomous agents |
| [`AI_CONTEXT.md`](AI_CONTEXT.md) | Universal | Fallback context for any AI tool |
| [`.github/copilot-instructions.md`](.github/copilot-instructions.md) | GitHub Copilot | Instructions for VS Code Copilot |


## 📚 Detailed Guides (Skills)

Located in [`.cursor/skills/`](.cursor/skills/):

- [`00-jmix-overview.md`](.cursor/skills/00-jmix-overview.md) — What is Jmix (vs Spring Boot)
- [`01-entities-skill.md`](.cursor/skills/01-entities-skill.md) — How to create entities
- [`02-views-skill.md`](.cursor/skills/02-views-skill.md) — How to create UI views
- [`03-services-skill.md`](.cursor/skills/03-services-skill.md) — Business logic & DataManager
- [`04-security-skill.md`](.cursor/skills/04-security-skill.md) — Roles & policies
- [`05-testing-skill.md`](.cursor/skills/05-testing-skill.md) — Unit & UI testing
- [`06-liquibase-skill.md`](.cursor/skills/06-liquibase-skill.md) — Database migrations

## 🔧 Project Structure

```
jmix-common-project/
├── src/main/java/.../entity/      # Jmix Entities
├── src/main/java/.../view/        # Vaadin UI Controllers
├── src/main/resources/.../view/   # XML Descriptors
├── src/main/java/.../security/    # Resource Roles
└── src/main/resources/liquibase/  # DB Migrations
```

## 🔌 RAG-based MCP

If you have `jmix-rag-mcp-search` configured in your IDE:
- AI assistants will use it to find Jmix-specific code snippets.
- It replaces general web search for framework-related questions.
- Reference: See `.cursorrules` or `CLAUDE.md`.
