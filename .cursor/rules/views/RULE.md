---
description: Rules for creating Vaadin Flow UI views with XML descriptors and Java controllers
globs: ["**/view/**"]
alwaysApply: false
---

# Jmix Views Rules

## Scope
Applies to `src/main/java/**/view/` and `src/main/resources/**/view/`

## List View Pattern
- Java: `extends StandardListView<Entity>`
- Annotations: `@Route`, `@ViewController`, `@ViewDescriptor`, `@LookupComponent`
- XML: `<collection>` container + `<dataLoadCoordinator auto="true"/>`
- Actions: create/edit/remove on dataGrid

## Detail View Pattern
- Java: `extends StandardDetailView<Entity>`
- Annotations: `@Route`, `@ViewController`, `@ViewDescriptor`, `@EditedEntityContainer`
- XML: `<instance>` container + formLayout
- Actions: saveClose/close

## Dependency Injection in Views

### UI Components from XML — `@ViewComponent`
```java
@ViewComponent
private DataGrid<Order> ordersDataGrid;
@ViewComponent
private CollectionLoader<Order> ordersDl;
@ViewComponent
private MessageBundle messageBundle;
@ViewComponent
private ComboBox<OrderStatus> statusFilter;
```

### Services and Infrastructure — `@Autowired`
```java
@Autowired
private DialogWindows dialogWindows;
@Autowired
private DataManager dataManager;
@Autowired
private Notifications notifications;
@Autowired
private Messages messages;
```

### Exception: DataContext
```java
@ViewComponent  // NOT @Autowired!
private DataContext dataContext;
```

### Event Handlers and Delegates
- `@Subscribe` for event handlers
- `@Install` for delegates
- `@Supply` for renderers

## Key Events
- `InitEntityEvent<T>` — set defaults for new entities
- `ValidationEvent` — cross-field validation
- `BeforeCloseEvent` — prevent close (unsaved changes)

## Opening Views
- `ViewNavigators` — full navigation (URL changes)
- `DialogWindows` — modal dialogs (no URL)

## Fragments
- Use `@FragmentDescriptor` (NOT `@ViewController`)
- Include via `class=` attribute (NOT `screen=`)
- `provided="true"` for data from host view
- No BeforeShow → use `@Subscribe(target = Target.HOST_CONTROLLER)`

## Layout Rules
- Always set `padding="false"` on vbox/hbox
- Buttons panel: `<hbox classNames="buttons-panel">`
- Sections: `<details summaryText="...">` instead for grouping
- Forms: `<formLayout responsiveSteps="...">`

## After Creating View
1. Add menu entry in `menu.xml`
2. Add messages for title/labels in ALL locale files
3. Add `@ViewPolicy` to relevant roles

## Forbidden
- Business logic in controllers (move to services)
- EntityManager usage (use DataManager)
- Direct transactions
- Hardcoded UI text (use `msg://` prefix)
- Adding messages to only one locale file
