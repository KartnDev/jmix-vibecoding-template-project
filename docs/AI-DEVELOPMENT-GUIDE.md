# AI-Assisted Development: Technical Guide

Comprehensive guide for AI-assisted Jmix development including MCP configuration, model comparison, and benchmarks.

---

## Error Distribution Analysis

Empirical data from production deployments shows the following error distribution in AI-generated code:

| Error Type | Percentage | Detection Method |
|------------|------------|------------------|
| Compile-time | ~25% | Build process |
| Design-time | ~25% | Application startup |
| Runtime | ~50% | Manual testing / MCP |

Runtime errors constitute the majority of issues and require automated verification mechanisms.

## Model Compatibility

### Tier 1: Full Jmix Support
- Claude Opus 4.5
- Gemini Pro

Characteristics: native understanding of Jmix 2.x annotations, Vaadin Flow components, minimal hallucination rate.

### Tier 2: Partial Support (context required)
- Claude Sonnet
- GPT-4/GPT-5
- Gemini Flash

Common issues: incorrect annotation usage (`@GeneratedValue` instead of `@JmixGeneratedValue`), missing `@InstanceName`.

### Tier 3: Limited Support
- Codestral
- Qwen
- Llama 3

Characteristics: high hallucination rate, requires extensive contextual documentation.

RAG dependency is inversely proportional to model tier: Opus generates correct Jmix code without additional context, while Llama 3 without RAG produces predominantly invalid code.

### Entity Generation Example

```
Correct (Opus):   @JmixEntity + @Entity + UUID + @Version + @InstanceName
Incorrect (GPT):  @Entity + @GeneratedValue (missing @JmixEntity)
```

Entities without `@JmixEntity` annotation compile successfully but are not recognized by DataManager or Jmix Studio.

## Platform Comparison

| Platform | Editor | Capabilities | Limitations | Pricing |
|----------|--------|--------------|-------------|---------|
| Cursor | VS Code | Sub-agents, multi-model evaluation | No Jmix Studio integration | ~$200/mo + API |
| Claude Code | Any | Terminal agent, parallel sessions | Context loss in long sessions | ~$200/mo (Max) |
| Junie | IntelliJ IDEA | Native IDE integration | Less proactive | Similar to Cursor |
| Continue | Any | Local deployment, data privacy | Suboptimal tool calling | Varies |

## MCP Configuration

Model Context Protocol enables automated verification through IDE and browser integration.

### Required MCP Servers

1. **JetBrains MCP**: IDE integration for compilation errors, Studio warnings, code navigation
2. **Playwright MCP**: browser automation for UI verification

### Configuration: ~/.claude/settings.json

```json
{
  "mcpServers": {
    "jetbrains": {
      "command": "npx",
      "args": ["-y", "@jetbrains/mcp-proxy"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    },
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "jmix-rag": {
      "type": "sse",
      "url": "https://mcp.jmixaiassistant.io/sse"
    }
  }
}
```

`context7` and `jmix-rag` are optional — required for models with limited Jmix knowledge (Sonnet, local models).

### Alternative: SSE Transport (IDEA 2025.2+)

```json
{
  "mcpServers": {
    "jetbrains": {
      "type": "sse",
      "url": "http://localhost:65520/sse"
    }
  }
}
```

### Server Responsibilities

- **jetbrains** — IntelliJ IDEA integration: compilation errors, Studio warnings, code navigation
- **playwright** — browser automation: page navigation, clicks, screenshots, UI verification
- **context7** (optional) — universal documentation search, keyword-based. Jmix support contributed by the framework team
- **jmix-rag** (optional) — Jmix-specific vector store with documentation and UI-samples. Embedding-based search, returns top-4 relevant results. Higher accuracy for Jmix-specific queries compared to keyword search

### CLI Installation

Requirements: Node.js 18+

```bash
claude mcp add jetbrains -- npx -y @jetbrains/mcp-proxy
claude mcp add playwright -- npx -y @playwright/mcp

# optional, for models with limited Jmix knowledge:
claude mcp add context7 -- npx -y @upstash/context7-mcp

# jmix-rag uses SSE, add to settings.json manually:
# "jmix-rag": { "type": "sse", "url": "https://mcp.jmixaiassistant.io/sse" }
```

### IDE Configuration

Path: File → Settings → Tools → AI Coding Assistants → MCP Servers

### Performance Characteristics

Playwright MCP latency: 20-60 seconds per interaction. Recommended for pre-commit verification, not iterative development.

## Context File Configuration

### Files by Platform

| Platform | File |
|----------|------|
| Claude Code | `CLAUDE.md` |
| Cursor | `.cursorrules` |
| Junie | `.junie/guidelines.md` |
| Multi-platform | `AGENTS.md` |

### Minimal CLAUDE.md Template

```markdown
# Project: Jmix 2.x Application

## Stack
- Java 17, Spring Boot, Vaadin Flow
- Database: HSQLDB (dev), PostgreSQL (prod)
- Run: `./gradlew bootRun` (localhost:8080, admin/admin)

## Rules
- Entity: @JmixEntity + UUID (@JmixGeneratedValue) + @Version + @InstanceName
- Data access: DataManager only (NOT EntityManager)
- DI: constructor injection (no field @Autowired)
- Views: XML descriptor + Java controller pair
- All UI text: msg:// keys, add to ALL locale files

## Forbidden
- Lombok on entities
- Business logic in views
- Edits in frontend/generated/
```

## Prompt Structure

### Recommended Format

```
1. Context acquisition: review CLAUDE.md, project structure, existing entities and views.

2. Entity specification:
   - Field definitions with types
   - Relationships
   - Enum definitions

3. View requirements:
   - List view
   - Detail view
   - Menu placement

4. Verification steps:
   - IDEA MCP: get_file_problems
   - Playwright: UI interaction verification

5. Commit criteria
```

## TDD Integration

TDD methodology is recommended for AI-assisted development:

1. Generate tests for existing code before modifications
2. Validate AI-generated changes against existing test suite
3. Automatic regression detection

Rationale: subsequent AI iterations may introduce regressions in previously working code.

## Limitations

### Unsuitable Use Cases

| Category | Issue | Recommendation |
|----------|-------|----------------|
| Complex business logic | Incorrect algorithm implementation | Manual implementation |
| External integrations | Unknown API specifications | Provide detailed API documentation |
| Legacy code (no tests) | Unpredictable side effects | Add test coverage first |
| Security-critical code | Requires manual audit | Mandatory human review |

### Data Privacy

Cloud-based LLMs transmit code to external servers. For NDA projects, use local model deployment or sanitize context.

## Required Components

### Minimal Configuration

1. **Claude Opus 4.5** — primary model with Jmix knowledge (alternative: Sonnet with full context)
2. **Claude Code** — terminal agent with file system access
3. **Playwright MCP** — automated UI verification
4. **JetBrains MCP** — IDE warnings and compilation errors
5. **AI-README** — project rules and constraints

### Optional Components

- **rules/, skills/** directories — extended documentation for large projects
- **Multiple AI-README files** — only for multi-platform deployments
- **Context7** (`@upstash/context7-mcp`) — universal documentation search MCP server. Jmix support contributed by the framework team. Keyword-based search, quick setup. Dependency inversely proportional to model capability: unnecessary for Opus, essential for local models
- **Jmix RAG MCP** (`mcp.jmixaiassistant.io/sse`) — Jmix-specific vector store. Contains Jmix 2.x documentation and code snippets from UI-samples. Embedding-based search returns top-4 relevant results. Higher accuracy for Jmix-specific queries compared to Context7 keyword search
- **Custom MCP servers** — GitHub, Sentry, databases — add as needed

## Prompt Engineering

Modern LLMs (2024+) implement internal prompt optimization (ReAct, Thinking mode). Explicit role instructions ("act as senior developer") provide minimal benefit.

Exception: role assignment for specialized agent tasks (security review, test generation) benefits from explicit role definition.

## Scaling: Multi-Agent Architecture

### Parallel Agent Deployment

Requirements:
- Modular architecture with clear boundaries
- Separate working directories per agent
- No shared file modifications

### Orchestration Pattern

Primary model delegates to specialized sub-agents:
- Architecture agent
- Implementation agent
- Review agent

Supported in Cursor via sub-agent configuration.

## Setup Time Estimates

| Configuration | Duration |
|---------------|----------|
| CLAUDE.md only | ~15 minutes |
| Full MCP setup | ~60 minutes |

---

## Skills and Rules

### Concept

**Skills** and **Rules** are structured documentation files that provide context to AI agents. They solve the problem of LLMs hallucinating APIs or using incorrect patterns for domain-specific frameworks like Jmix.

- **Skills** (`SKILL.md`) — procedural guides explaining *how* to perform a task. Contain step-by-step instructions, code templates, and examples. Read on-demand when agent needs to perform a specific task.
- **Rules** (`RULE.md`) — constraints and requirements that *must* be followed. Contain forbidden patterns, required annotations, naming conventions. Applied automatically based on file path context.

### When to Use

| Scenario | Use |
|----------|-----|
| Agent needs to create an entity | Reads `skills/entities/SKILL.md` for step-by-step guide |
| Agent edits file in `**/entity/**` | Platform auto-applies `rules/entities/RULE.md` |
| Agent asks "how does Jmix work" | Reads `skills/jmix-overview/SKILL.md` |
| Any file modification | Platform auto-applies `rules/jmix-overview/RULE.md` (always active) |

### Directory Structure

```
ai-docs/
├── skills/                    # Procedural guides (read on-demand)
│   ├── entities/SKILL.md      # How to create entities
│   ├── views/SKILL.md         # How to create views
│   ├── services/SKILL.md      # How to write services
│   └── ...
└── rules/                     # Constraints (auto-applied by context)
    ├── jmix-overview/RULE.md  # Always active
    ├── entities/RULE.md       # Active for **/entity/**
    ├── views/RULE.md          # Active for **/view/**
    └── ...
```

### Skills (Detailed Guides)

Located in `ai-docs/skills/` — each skill is a folder with `SKILL.md`:

| Folder | Content |
|--------|---------|
| `jmix-overview/` | Jmix architecture, differences from Spring Boot, core concepts |
| `entities/` | Entity creation: annotations, UUID, versioning, relationships |
| `views/` | UI views: XML descriptors, Java controllers, data binding |
| `services/` | Business logic: DataManager, transactions, query syntax |
| `security/` | Resource roles, row-level security, policies |
| `testing/` | Unit tests, integration tests, UI tests with Playwright |
| `liquibase/` | Database migrations: changesets, UUID types, foreign keys |
| `dto/` | Data Transfer Objects for REST API |
| `enums/` | Enumerations with EnumClass interface |
| `fetchplans/` | Lazy loading, fetch plans, avoiding N+1 queries |
| `i18n/` | Message bundles, localization, msg:// keys |

### Rules (Context-Specific Constraints)

Located in `ai-docs/rules/` — each rule is a folder with `RULE.md`:

| Folder | Activation Context | Key Constraints |
|--------|-------------------|-----------------|
| `jmix-overview/` | Always | DataManager only, no EntityManager |
| `entities/` | `**/entity/**` | @JmixEntity required, UUID + Version |
| `views/` | `**/view/**` | XML + Java pair, msg:// for all text |
| `services/` | `**/service/**` | Constructor injection, no @Autowired |
| `security/` | `**/security/**` | @ResourceRole format, policy types |
| `testing/` | `**/test/**` | @SpringBootTest configuration |
| `liquibase/` | `**/liquibase/**` | ${uuid.type}, include in changelog.xml |
| `dto/` | `**/dto/**` | No JPA annotations on DTOs |
| `enums/` | Enum files | EnumClass interface, @DefaultValue |
| `fetchplans/` | Data loading | Fetch plan naming, lazy loading rules |
| `i18n/` | Messages files | All locales, key naming conventions |

### Platform Support

| Platform | Skills Support | Rules Support | Auto-context |
|----------|---------------|---------------|--------------|
| Cursor | Yes (`ai-docs/skills/`) | Yes (`ai-docs/rules/`) | By file path |
| Claude Code | Manual reference | Manual reference | None |
| Junie | Yes (`ai-docs/skills/`) | Yes (`ai-docs/rules/`) | Limited |
| Continue | Yes | Limited | Manual |

For universal compatibility, use `ai-docs/` directory — readable by all platforms including those that cannot access dotfiles.

## Project Structure

```
jmix-vibecoding-template-project/
├── src/main/java/.../entity/      # Jmix Entities
├── src/main/java/.../view/        # Vaadin UI Controllers
├── src/main/java/.../security/    # Resource Roles
├── src/main/java/.../service/     # Business Logic
├── src/main/resources/.../view/   # XML Descriptors
├── src/main/resources/liquibase/  # DB Migrations
├── ai-docs/skills/                # AI Skills (universal)
└── ai-docs/rules/                 # AI Rules (universal)
```

---

## AI Agents Benchmark Results

Extensive testing of 14 AI agent configurations on a standard task: **"Write a petclinic application"**.

### Final Rankings

| Rank | Agent | Score | Compiles | Cost | Verdict |
|------|-------|-------|----------|------|---------|
| 1 | **Cursor (Opus 4.5)** | **71/100** | Yes | ~$2-3 | Best overall |
| 2 | Cursor (Sonnet 4.5) | 70/100 | Yes | ~$1-2 | Best one-shot |
| 3 | Continue (Opus 4.5) | 68/100 | Yes | **$8** | Expensive |
| 4 | Junie (Gemini 3 Flash) | 66/100 | Yes | Low | Best budget |
| 5 | Claude Code (Sonnet 4.5) | 65/100 | Yes | ~$2 | Ignores rules |
| 6 | Claude Code (Haiku 3.5) | 57/100 | Yes | Cheap | Best tiny model |
| 7 | Cursor (ChatGPT 5.2) | 56/100 | Yes | ~$1 | 0 runtime errors |
| 8 | Junie (Opus 4.5) | 52/100 | Yes | High | Platform limits |
| 9 | Claude Code (Opus 4.5) | 51/100 | Partial | ~$3 | Tested without CLAUDE.md |
| 10 | Cursor (Gemini 3 Flash) | 46/100 | Partial | Low | Confused |
| 11 | Junie (Sonnet 4.5) | 45/100 | Yes | Medium | Failed i18n |
| 11 | Junie (GPT-5.1 Codex) | 45/100 | Partial | Medium | 6 prompts needed |
| 13 | Cursor (Default) | 40/100 | Partial | Low | Avoid |
| 14 | Continue (Haiku 3.5) | 30/100 | No | ~$2-3 | **FAIL** |

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
| Junie + Gemini 3 Flash | 66 | Low | Best value |
| Cursor + Sonnet 4.5 | 70 | Medium | Great |
| Claude Code + Haiku | 57 | Low | Good for tiny model |
| Continue + Opus 4.5 | 68 | High ($8!) | Poor value |

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
