# AI_CONTEXT.md — Universal AI Context

## Project
- Type: Fullstack Jmix 2.7.x sample (Java 17, Spring Boot, Vaadin Flow)
- Database: HSQLDB (dev)
- Run: `./gradlew bootRun` (http://localhost:8080, admin/admin)

## Architecture
- Entity: `@JmixEntity`, UUID + `@JmixGeneratedValue`, `@Version`, `@InstanceName`
- Data: `DataManager` (NOT EntityManager), fetch plans
- UI: Vaadin Flow Views (XML + Java), `StandardListView` / `StandardDetailView`
- Security: `@ResourceRole`, `@ViewPolicy`, `@MenuPolicy`
- DB: Liquibase changelog (UUID PK, VERSION, include in `changelog.xml`)

## Patterns
- DI: constructor injection only
- No Lombok on entities
- Business logic in services, not in views

## Files by Vendor
- Cursor: `.cursorrules`, `.cursor/rules/*.mdc`, `.cursor/skills/*.md`
- Claude: `CLAUDE.md`
- Codex/Agents: `AGENTS.md`
- Copilot: `.github/copilot-instructions.md`
- Windsurf: `.windsurfrules`

## MCP (optional)
If `jmix-rag-mcp-search` is available:
- Use it for Jmix-specific questions
- Prefer MCP over web search

## Checklists
### Minimum (quick start)
- `.cursorrules` + `CLAUDE.md`
- `AI_CONTEXT.md`

### Optimum (full)
- Skills + Rules
- Copilot/Windsurf files
- `docs/CODE_STYLE.md`

### Entity/View/Role (before merge)
- Entity: UUID + Version + InstanceName; Liquibase + include; messages
- View: XML + Java; menu entry; messages
- Role: entity policies + view/menu policies
