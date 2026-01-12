# Skill: Jmix Overview

## Что такое Jmix
High-productivity платформа (Spring Boot + Vaadin Flow) для enterprise приложений.

## Jmix ≠ обычный Spring Boot
| Aspect | Spring Boot | Jmix |
|--------|-------------|------|
| Entity | @Entity | @JmixEntity + @Entity |
| ID     | Long       | UUID + @JmixGeneratedValue |
| Data   | JpaRepository | DataManager |
| UI     | REST+SPA   | Vaadin Flow (XML+Java) |
| Security | Manual  | @ResourceRole annotations |

## Ключевые концепты
- @JmixEntity, @InstanceName, @Version
- DataManager для CRUD
- Views: XML descriptor + Java controller
- Security: ResourceRole, RowLevelRole
- Liquibase: UUID PK, VERSION, include в changelog.xml
