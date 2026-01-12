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

## When Asked to Create
### Entity
- Java class with UUID + Version + InstanceName
- Liquibase changelog + include in `changelog.xml`
- Messages in `messages_en.properties`

### View
- XML descriptor + Java controller
- Menu entry in `menu.xml`
- Messages for title/labels

### Role
- `@ResourceRole` with entity/view/menu policies

## Forbidden
- Lombok on entities
- Field `@Autowired`
- EntityManager
- Business logic in views
- Edits in `frontend/generated/`

## References
Detailed guides (read when performing specific tasks):
- Jmix overview: `.cursor/skills/00-jmix-overview.md`
- Entity creation: `.cursor/skills/01-entities-skill.md`
- View creation: `.cursor/skills/02-views-skill.md`
- Services: `.cursor/skills/03-services-skill.md`
- Security: `.cursor/skills/04-security-skill.md`
- Testing: `.cursor/skills/05-testing-skill.md`
- Liquibase: `.cursor/skills/06-liquibase-skill.md`
- DTOs: `.cursor/skills/07-dto-skill.md`
- Enums: `.cursor/skills/08-enums-skill.md`
- Fetch Plans: `.cursor/skills/09-fetchplans-skill.md`

## Files by Vendor
- Cursor: `.cursorrules`, `.cursor/rules/*.mdc`
- Claude: `CLAUDE.md`
- Codex/Agents: `AGENTS.md`
- Copilot: `.github/copilot-instructions.md`

## MCP (optional)
- If `jmix-rag-mcp-search` is available, use it for Jmix-specific questions instead of web search.
- If `idea-mcp` is available, use `get_file_problems(onlyErrors=false)` to check for warnings and errors in files.
