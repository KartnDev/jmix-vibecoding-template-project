# CODE_STYLE — Jmix Fullstack

## Форматирование
- Отступ 4 пробела, без табов
- Макс. длина строки: 120
- Импорты без wildcard

## Именование
- Классы: PascalCase (`Customer`)
- Таблицы/колонки: UPPER_SNAKE_CASE (`CUSTOMER`, `FIRST_NAME`)
- Views: `EntityNameListView.java` / `entity-name-list-view.xml`

## DI
- Только конструкторная инъекция
- Запрещено: field `@Autowired`

## Entities
- `@JmixEntity` + `@Entity`
- ID: `UUID @JmixGeneratedValue`
- `@Version` обязательно
- `@InstanceName` для отображения
- Без Lombok

## Data Access
- Использовать `DataManager`
- Не использовать EntityManager/JpaRepository
- Указывать fetch plans для выборок

## Views (Vaadin Flow)
- XML + Java контроллер
- `StandardListView` / `StandardDetailView`
- Data containers + actions
- Обновлять `menu.xml`, `messages_en.properties`

## Security
- `@ResourceRole` с entity policies
- `@ViewPolicy` / `@MenuPolicy`
- Не хранить креды в коде

## Liquibase
- UUID PK, VERSION колонка
- Каждый changeset в `.../liquibase/changelog/`
- Добавлять в `changelog.xml`

## Антипаттерны (запрещено)
- Lombok на сущностях
- Field `@Autowired`
- EntityManager
- Логика в view контроллерах
- Изменение `frontend/generated/`
