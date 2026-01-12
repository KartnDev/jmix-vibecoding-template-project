# Skill: Views (Vaadin Flow)

## Описание
Создание экранов (List/Detail) в Jmix.

## List View (паттерн)
Java: `StandardListView<Entity>`
XML: data containers + dataLoadCoordinator + actions (create/edit/remove)

## Detail View (паттерн)
Java: `StandardDetailView<Entity>`
XML: instance container, form fields, save/close actions

## Обязательные шаги
- XML в `resources/.../view/ENTITY/`
- Java в `java/.../view/ENTITY/`
- Меню: добавить в `menu.xml`
- Сообщения: `messages_en.properties`

## Инъекции и события
- `@ViewComponent` для UI компонентов
- `@Subscribe` для событий

## Чеклист
- Пара XML+Java создана
- Data containers настроены
- Actions подключены
- Меню и сообщения обновлены

## Запрещено
- Бизнес-логика в контроллере (вынести в сервис)
- EntityManager
- Транзакции в view
