# PROJECT_CONTEXT — Jmix Fullstack

## Назначение
Учебный fullstack пример Jmix 2.7.x (Java 17, Spring Boot, Vaadin Flow).

## Архитектура (высокоуровнево)
- Entity слой: `@JmixEntity` с UUID PK, Version, InstanceName
- Data слой: `DataManager`, fetch plans
- UI: Vaadin Flow Views (XML + Java, StandardListView/DetailView)
- Security: `@ResourceRole`, `@ViewPolicy`, `@MenuPolicy`
- DB: Liquibase changelog (включено в `changelog.xml`)

## Каталоги
- `src/main/java/.../entity/` — сущности
- `src/main/java/.../view/` — контроллеры экранов
- `src/main/resources/.../view/` — XML дескрипторы
- `src/main/java/.../security/` — роли
- `src/main/resources/.../liquibase/changelog/` — миграции
- `src/main/resources/.../menu.xml` — меню
- `src/main/resources/.../messages_en.properties` — сообщения

## Команды
- Запуск: `./gradlew bootRun`
- Тесты: `./gradlew test`
- Сборка: `./gradlew build`

## Jmix правила (кратко)
- ID всегда UUID + `@JmixGeneratedValue`
- `@Version` обязателен
- `DataManager` вместо EntityManager
- Views всегда пара XML + Java
- Добавлять меню и messages для новых экранов

## MCP (опционально)
Если подключен `jmix-rag-mcp-search`, используйте его для Jmix-вопросов вместо веб-поиска.
