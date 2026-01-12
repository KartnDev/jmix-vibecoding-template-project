# Skill: Views (Screens)

## Description
Creating Vaadin Flow UI views with XML descriptors and Java controllers.

## List View

### Java Controller
```java
package com.company.project.view.customer;

import com.company.project.entity.Customer;
import com.company.project.view.main.MainView;
import com.vaadin.flow.router.Route;
import io.jmix.flowui.view.*;

@Route(value = "customers", layout = MainView.class)
@ViewController("Customer.list")
@ViewDescriptor("customer-list-view.xml")
@LookupComponent("customersDataGrid")
@DialogMode(width = "64em")
public class CustomerListView extends StandardListView<Customer> {
}
```

### XML Descriptor (`customer-list-view.xml`)
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<view xmlns="http://jmix.io/schema/flowui/view"
      title="msg://customerListView.title"
      focusComponent="customersDataGrid">
    <data readOnly="true">
        <collection id="customersDc" class="com.company.project.entity.Customer">
            <fetchPlan extends="_base"/>
            <loader id="customersDl">
                <query>
                    <![CDATA[select e from Customer e]]>
                </query>
            </loader>
        </collection>
    </data>
    <facets>
        <dataLoadCoordinator auto="true"/>
    </facets>
    <actions>
        <action id="selectAction" type="lookup_select"/>
        <action id="discardAction" type="lookup_discard"/>
    </actions>
    <layout>
        <genericFilter id="genericFilter" dataLoader="customersDl"/>
        <hbox id="buttonsPanel" classNames="buttons-panel">
            <button id="createBtn" action="customersDataGrid.create"/>
            <button id="editBtn" action="customersDataGrid.edit"/>
            <button id="removeBtn" action="customersDataGrid.remove"/>
        </hbox>
        <dataGrid id="customersDataGrid" dataContainer="customersDc" width="100%" minHeight="20em">
            <actions>
                <action id="create" type="list_create"/>
                <action id="edit" type="list_edit"/>
                <action id="remove" type="list_remove"/>
            </actions>
            <columns resizable="true">
                <column property="name"/>
                <column property="email"/>
            </columns>
        </dataGrid>
    </layout>
</view>
```

## Detail View

### Java Controller
```java
package com.company.project.view.customer;

import com.company.project.entity.Customer;
import com.company.project.view.main.MainView;
import com.vaadin.flow.router.Route;
import io.jmix.flowui.view.*;

@Route(value = "customers/:id", layout = MainView.class)
@ViewController("Customer.detail")
@ViewDescriptor("customer-detail-view.xml")
@EditedEntityContainer("customerDc")
public class CustomerDetailView extends StandardDetailView<Customer> {
}
```

### XML Descriptor (`customer-detail-view.xml`)
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<view xmlns="http://jmix.io/schema/flowui/view"
      title="msg://customerDetailView.title"
      focusComponent="form">
    <data>
        <instance id="customerDc" class="com.company.project.entity.Customer">
            <fetchPlan extends="_base"/>
            <loader/>
        </instance>
    </data>
    <facets>
        <dataLoadCoordinator auto="true"/>
    </facets>
    <actions>
        <action id="saveAction" type="detail_saveClose"/>
        <action id="closeAction" type="detail_close"/>
    </actions>
    <layout>
        <formLayout id="form" dataContainer="customerDc">
            <textField id="nameField" property="name"/>
            <textField id="emailField" property="email"/>
        </formLayout>
        <hbox id="detailActions">
            <button id="saveAndCloseBtn" action="saveAction"/>
            <button id="closeBtn" action="closeAction"/>
        </hbox>
    </layout>
</view>
```

## Menu Entry
Add to `menu.xml`:
```xml
<item id="Customer.list" view="Customer.list"/>
```

## Messages
Add to `messages_en.properties`:
```properties
com.company.project.view.customer/customerListView.title=Customers
com.company.project.view.customer/customerDetailView.title=Customer
```

## Dependency Injection in Views

### UiScope
Views in Jmix use **`UiScope`** — a Spring scope where beans exist for the lifetime of a single UI (browser tab).
This means view controllers are NOT singletons — each view instance has its own state.

### Spring Beans vs View Components

| What | Annotation | Example |
|------|------------|---------|
| Spring beans (services, DataManager) | `@Autowired` | `@Autowired DataManager dataManager` |
| UI components from XML | `@ViewComponent` | `@ViewComponent DataGrid<Customer> grid` |
| Data containers | `@ViewComponent` | `@ViewComponent CollectionContainer<Customer> customersDc` |
| Data loaders | `@ViewComponent` | `@ViewComponent CollectionLoader<Customer> customersDl` |
| DataContext | `@ViewComponent` | `@ViewComponent DataContext dataContext` |

### Example
```java
public class CustomerListView extends StandardListView<Customer> {

    // Spring beans — use @Autowired
    @Autowired
    private DataManager dataManager;
    
    @Autowired
    private CustomerService customerService;

    // View components from XML — use @ViewComponent
    @ViewComponent
    private DataGrid<Customer> customersDataGrid;

    @ViewComponent
    private CollectionContainer<Customer> customersDc;

    @ViewComponent
    private CollectionLoader<Customer> customersDl;

    // DataContext for managing entity state
    @ViewComponent
    private DataContext dataContext;
}
```

## Event Handlers (@Subscribe)

`@Subscribe` — listen to view lifecycle events or component events.

```java
// View lifecycle events (no target specified)
@Subscribe
public void onInit(InitEvent event) {
    // Called when view is created, before UI components are ready
}

@Subscribe
public void onBeforeShow(BeforeShowEvent event) {
    // Called before view is shown, data is loaded
}

@Subscribe
public void onReady(ReadyEvent event) {
    // Called when view is fully initialized and visible
}

// Component events (target = component id)
@Subscribe("customersDataGrid.create")
public void onCreateAction(ActionPerformedEvent event) {
    // Handle action click
}

@Subscribe(id = "customersDataGrid", target = Target.DATA_CONTEXT)
public void onPreCommit(DataContext.PreCommitEvent event) {
    // Before saving
}
```

## Delegates (@Install)

`@Install` — provide custom implementation for component behavior.

```java
// Custom data loader
@Install(to = "customersDl", target = Target.DATA_LOADER)
private List<Customer> customersDlLoadDelegate(LoadContext<Customer> loadContext) {
    return dataManager.loadList(loadContext);
}

// Custom validator
@Install(to = "nameField", subject = "validator")
private void nameFieldValidator(String value) {
    if (value != null && value.length() < 3) {
        throw new ValidationException("Name must be at least 3 characters");
    }
}

// Custom item label generator
@Install(to = "customerField", subject = "itemLabelGenerator")
private String customerFieldItemLabelGenerator(Customer customer) {
    return customer.getName() + " (" + customer.getEmail() + ")";
}
```

## Renderers (@Supply)

`@Supply` — provide renderers for dataGrid columns, comboBox items, etc.
**Note:** This is different from `@Install`!

```java
@Autowired
private UiComponents uiComponents;

// DataGrid column renderer (checkbox)
@Supply(to = "customersDataGrid.active", subject = "renderer")
private Renderer<Customer> activeColumnRenderer() {
    return new ComponentRenderer<>(
            () -> {
                JmixCheckbox checkbox = uiComponents.create(JmixCheckbox.class);
                checkbox.setReadOnly(true);
                return checkbox;
            },
            (checkbox, customer) -> checkbox.setValue(customer.getActive())
    );
}

// DataGrid column renderer (badge/span)
@Supply(to = "customersDataGrid.status", subject = "renderer")
private Renderer<Customer> statusColumnRenderer() {
    return new ComponentRenderer<>(customer -> {
        Span badge = new Span(customer.getStatus().name());
        badge.getElement().getThemeList().add("badge");
        return badge;
    });
}

// ComboBox item renderer
@Supply(to = "departmentField", subject = "renderer")
private Renderer<Department> departmentRenderer() {
    return new ComponentRenderer<>(department -> {
        Icon icon = VaadinIcon.USERS.create();
        HorizontalLayout layout = uiComponents.create(HorizontalLayout.class);
        layout.setPadding(false);
        layout.add(icon, new Span(department.getName()));
        return layout;
    });
}

// Text renderer (simple)
@Supply(to = "customersDataGrid.fullName", subject = "renderer")
private Renderer<Customer> fullNameRenderer() {
    return new TextRenderer<>(customer -> 
            customer.getFirstName() + " " + customer.getLastName()
    );
}
```

### Renderer Types
| Type | Use case |
|------|----------|
| `TextRenderer` | Simple text formatting |
| `ComponentRenderer` | Custom UI components (checkbox, badge, icon) |
| `NumberRenderer` | Number formatting |
| `LocalDateRenderer` | Date formatting |

### Performance Warning
`ComponentRenderer` creates UI component for each row — avoid for large datasets!

## Checklist
- [ ] Java controller extends `StandardListView` or `StandardDetailView`
- [ ] XML descriptor created in `resources/.../view/`
- [ ] `@Route`, `@ViewController`, `@ViewDescriptor` annotations
- [ ] Data containers and loaders defined
- [ ] Menu entry added to `menu.xml`
- [ ] Messages for title/labels added

## Forbidden
- Business logic in view controllers (move to services)
- EntityManager usage (use DataManager)
- Direct database transactions
