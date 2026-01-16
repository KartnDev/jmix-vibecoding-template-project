# Jmix Common Project (AI Reference)

A **Fullstack Jmix 2.7.x Application** (Java 17, Spring Boot, Vaadin Flow) serving as a **Reference Implementation for AI-Assisted Development**.

Copy AI configuration files from this repository into your own Jmix projects.

## Purpose

- Demonstrate how to configure AI coding assistants (Cursor, Claude, Copilot, Continue.dev, Codex) for Jmix
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

## Universal Documentation (`ai-docs/`)

> **Important:** The `ai-docs/` folder contains the same rules and skills as `.cursor/` but is accessible by ALL AI tools, including Junie (which cannot read dotfiles).

```
ai-docs/
├── rules/          # Context-specific rules
│   ├── entities/RULE.md
│   ├── views/RULE.md
│   ├── liquibase/RULE.md
│   └── ...
└── skills/         # Detailed guides
    ├── entities/SKILL.md
    ├── views/SKILL.md
    ├── liquibase/SKILL.md
    └── ...
```

## MCP Servers (Optional)

| Server | Purpose |
|--------|---------|
| `jmix-rag-mcp` | Jmix-specific questions (instead of web search) |
| `jetbrains` (IDEA MCP) | Get file problems, navigate code |
| `playwright` | Browser verification, UI testing |

---

## AI Agents Benchmark Results

We conducted extensive testing of 14 AI agent configurations on a standard task: **"Write a petclinic application"**.

### Final Rankings

| Rank | Agent | Score | Compiles | Cost | Verdict |
|------|-------|-------|----------|------|---------|
| 🥇 | **Cursor (Opus 4.5)** | **71/100** | ✅ | ~$2-3 | Best overall |
| 🥈 | Cursor (Sonnet 4.5) | 70/100 | ✅ | ~$1-2 | Best one-shot |
| 🥉 | Continue (Opus 4.5) | 68/100 | ✅ | **$8** | Expensive |
| 4 | Junie (Gemini 3 Flash) | 66/100 | ✅ | Low | Best budget |
| 5 | Claude Code (Sonnet 4.5) | 65/100 | ✅ | ~$2 | Ignores rules |
| 6 | Claude Code (Haiku 3.5) | 57/100 | ✅ | Cheap | Best tiny model |
| 7 | Cursor (ChatGPT 5.2) | 56/100 | ✅ | ~$1 | 0 runtime errors |
| 8 | Junie (Opus 4.5) | 52/100 | ✅ | High | Platform limits |
| 9 | Claude Code (Opus 4.5) | 51/100 | ⚠️ | ~$3 | Tested without CLAUDE.md |
| 10 | Cursor (Gemini 3 Flash) | 46/100 | ⚠️ | Low | Confused |
| 11 | Junie (Sonnet 4.5) | 45/100 | ✅ | Medium | Failed i18n |
| 11 | Junie (GPT-5.1 Codex) | 45/100 | ⚠️ | Medium | 6 prompts needed |
| 13 | Cursor (Default) | 40/100 | ⚠️ | Low | Avoid |
| 14 | Continue (Haiku 3.5) | 30/100 | ❌ | ~$2-3 | **FAIL** |

### Key Findings

#### 1. Platform Matters More Than Model
Same model, different results:
- **Opus 4.5**: Cursor 71 vs Continue 68 vs Claude Code 51
- **Sonnet 4.5**: Cursor 70 vs Claude Code 65 vs Junie 45
- **Haiku 3.5**: Claude Code 57 vs Continue 30

#### 2. What Each Tool Auto-Reads

| Tool | Auto-reads | Notes |
|------|------------|-------|
| **Claude Code** | `CLAUDE.md` only | Case-sensitive! |
| **Cursor** | `AGENTS.md`, `.cursorrules`, `.cursor/` | Best context support |
| **Continue** | `AGENTS.md`, `.continuerules` | Expensive, reads skills |
| **Junie** | `.junie/guidelines.md` | Cannot read dotfiles! |
| **Copilot** | `.github/copilot-instructions.md` | Limited |

#### 3. Cost Efficiency

| Agent | Score | Cost | Value |
|-------|-------|------|-------|
| Junie + Gemini 3 Flash | 66 | 💰 Low | 🔥 Best value |
| Cursor + Sonnet 4.5 | 70 | 💰💰 | Great |
| Claude Code + Haiku | 57 | 💰 | Good for tiny model |
| Continue + Opus 4.5 | 68 | 💰💰💰💰 | Poor value ($8!) |

### Recommendations

| If you want... | Use |
|----------------|-----|
| **Best result** | Cursor + Opus/Sonnet |
| **Best budget** | Junie + Gemini 3 Flash |
| **Tiny model** | Claude Code + Haiku |
| **Avoid** | Continue (any model), Cursor Default |

### Evaluation Criteria

Each agent was scored on 10 metrics (100 points total):
1. **Compilation** (10) — Does it compile and run?
2. **Entities** (10) — All 6 entities with correct relations?
3. **Views** (10) — List + Detail views working?
4. **Liquibase** (10) — Correct schema, FK, indexes?
5. **i18n** (10) — All messages in correct format?
6. **Menu** (10) — Logical menu structure?
7. **Rules** (10) — Did it read and follow project rules?
8. **Quality** (10) — Clean code, Jmix best practices?
9. **Services** (5) — Business logic separation?
10. **Tests** (10) — Any tests created?

### Common Issues Found

| Issue | Agents Affected |
|-------|-----------------|
| Missing Visit→Vet relation | Cursor Default, Junie Sonnet, ChatGPT |
| Wrong entityPicker (no actions) | Cursor Default, Cursor Gemini Flash |
| Invented non-existent APIs | Continue Haiku |
| Forgot i18n messages | Junie Sonnet (0/10!) |
| Mixed Jmix 1.x/2.x syntax | Continue Haiku |
| `varchar(36)` instead of `${uuid.type}` | Multiple agents |

---

*Full benchmark details available in the research repository.*
