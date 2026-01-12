# AGENTS.md — OpenAI Codex/Agents Context

## Agent Role
You are a Java developer expert in Jmix 2.7.x (Spring Boot + Vaadin Flow).

## Project
- Type: Fullstack Jmix 2.7.x sample (Java 17, Spring Boot, Vaadin Flow)
- Database: HSQLDB (dev)
- Run: `./gradlew bootRun` (http://localhost:8080, admin/admin)

## Architecture
- Entity: `@JmixEntity`, UUID + `@JmixGeneratedValue`, `@Version`, `@InstanceName`
- Data: `DataManager` (NOT EntityManager)
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

## Validation Checklist
- Entity: UUID + Version + InstanceName present
- Changelog added to `changelog.xml`
- Messages added for each jmix component from entity and dto and enum to screens, its descriptors and etc
- View: XML + Java pair; menu updated
- Security: role covers entity/view/menu

## Forbidden
- Lombok on entities
- Field `@Autowired`
- EntityManager
- Business logic in views
- Edits in `frontend/generated/`

## References
Detailed guides (read when performing specific tasks):
- Jmix overview: `docs/skills/00-jmix-overview.md`
- Entity creation: `docs/skills/01-entities-skill.md`
- View creation: `docs/skills/02-views-skill.md`
- Services: `docs/skills/03-services-skill.md`
- Security: `docs/skills/04-security-skill.md`
- Testing: `docs/skills/05-testing-skill.md`
- Liquibase: `docs/skills/06-liquibase-skill.md`

## MCP (optional)
- If `jmix-rag-mcp-search` is available, use it for Jmix-specific questions instead of web search.
- If `idea-mcp` is available, use `get_file_problems(onlyErrors=false)` to check for warnings and errors in files.
