# Skill: Jmix Overview

## What is Jmix
High-productivity platform (Spring Boot + Vaadin Flow) for enterprise applications.
Jmix provides ready-to-use components for data access, UI, security, and more.

## Jmix vs Plain Spring Boot

| Aspect | Spring Boot | Jmix |
|--------|-------------|------|
| Entity | `@Entity` | `@JmixEntity` + `@Entity` |
| ID | Long / UUID | UUID + `@JmixGeneratedValue` |
| Data Access | JpaRepository / EntityManager | `DataManager` |
| UI | REST + SPA | Vaadin Flow (XML + Java) |
| Security | Manual / Spring Security | `@ResourceRole`, `@RowLevelRole` |
| DB Migrations | Flyway / manual | Liquibase (auto-generated) |

## Key Concepts

### Entities
- `@JmixEntity` + `@Entity` annotations together
- `@InstanceName` on display field (shown in UI references)
- `@Version` for optimistic locking
- UUID primary key with `@JmixGeneratedValue`

### Data Access
- Use `DataManager` (NOT EntityManager)
- Fetch plans control lazy loading
- LoadContext for complex queries

### Views (UI)
- XML descriptor + Java controller pair
- `StandardListView<T>` for lists
- `StandardDetailView<T>` for edit forms
- Data containers bind data to UI

### Security
- `@ResourceRole` for entity/view/menu access
- `@RowLevelRole` for row-level filtering
- Policies: `@EntityPolicy`, `@ViewPolicy`, `@MenuPolicy`

### Database
- Liquibase for migrations
- UUID primary key, VERSION column
- Include changelogs in `changelog.xml`

## Project Structure
```
src/main/java/.../
├── entity/           # @JmixEntity classes
├── view/             # UI controllers
├── security/         # Roles
└── service/          # Business logic

src/main/resources/.../
├── view/             # XML descriptors
├── liquibase/        # DB changelogs
├── menu.xml          # App menu
└── messages_en.properties
```
