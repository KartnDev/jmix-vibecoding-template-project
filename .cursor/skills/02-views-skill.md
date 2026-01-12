# Skill: Views (Screens)

## Description
Creating Vaadin Flow UI views with XML descriptors and Java controllers.

## View Lifecycle (Order)

| Order | Event | Purpose |
|-------|-------|---------|
| 1 | `InitEvent` | View created, components exist but data NOT loaded |
| 2 | `InitEntityEvent<T>` | (Detail only) Entity created/loaded. Set defaults here |
| 3 | `BeforeShowEvent` | Before visible, data loaders triggered |
| 4 | `ReadyEvent` | View fully initialized and visible |
| 5 | `AfterShowEvent` | After view shown, for analytics/logging |
| 6 | `BeforeCloseEvent` | Before close, can prevent (unsaved changes) |
| 7 | `AfterCloseEvent` | After closed, cleanup |

**Detail view specific:** `ValidationEvent`, `BeforeSaveEvent`, `AfterSaveEvent`

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

// InitEntityEvent — set defaults for NEW entities (detail views)
@Subscribe
public void onInitEntity(InitEntityEvent<Customer> event) {
    Customer customer = event.getEntity();
    customer.setStatus(CustomerStatus.NEW);
    customer.setCreatedDate(LocalDate.now());
}

// BeforeCloseEvent — prevent closing with unsaved changes
@Subscribe
public void onBeforeClose(BeforeCloseEvent event) {
    if (dataContext.hasChanges()) {
        event.preventClose();
        // Show confirmation dialog
    }
}

// ValidationEvent — cross-field validation (detail views)
@Subscribe
public void onValidation(ValidationEvent event) {
    Customer customer = getEditedEntity();
    if (customer.getEndDate().isBefore(customer.getStartDate())) {
        event.getErrors().add("End date must be after start date");
    }
}

// Component events (target = component id)
@Subscribe("customersDataGrid.create")
public void onCreateAction(ActionPerformedEvent event) {
    // Handle action click
}

// DataContext events
@Subscribe(target = Target.DATA_CONTEXT)
public void onPreCommit(DataContext.PreCommitEvent event) {
    // Before saving all entities
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

## Opening Views Programmatically

### ViewNavigators (URL changes, full navigation)
```java
@Autowired
private ViewNavigators viewNavigators;

// Navigate to list
viewNavigators.view(this, CustomerListView.class).navigate();

// Navigate to detail (edit)
viewNavigators.detailView(this, Customer.class)
        .editEntity(customer)
        .navigate();

// Navigate to detail (create)
viewNavigators.detailView(this, Customer.class)
        .newEntity()
        .withInitializer(c -> c.setStatus(CustomerStatus.NEW))
        .navigate();

// Lookup
viewNavigators.lookup(this, Customer.class)
        .withSelectHandler(customers -> { /* handle */ })
        .navigate();
```

### DialogWindows (Modal, no URL change)
```java
@Autowired
private DialogWindows dialogWindows;

// Open detail as dialog
dialogWindows.detail(this, Customer.class)
        .editEntity(customer)
        .withAfterCloseListener(e -> {
            if (e.closedWith(StandardOutcome.SAVE)) {
                customersDl.load(); // Refresh
            }
        })
        .open();

// Open lookup dialog
dialogWindows.lookup(this, Customer.class)
        .withSelectHandler(customers -> { /* handle */ })
        .open();
```

| ViewNavigators | DialogWindows |
|----------------|---------------|
| URL changes | No URL change |
| Browser history | Not in history |
| Full page | Modal overlay |

## Standard Actions

| Type | Purpose |
|------|---------|
| `list_create` | Create new entity |
| `list_edit` | Edit selected |
| `list_remove` | Remove selected |
| `detail_saveClose` | Save and close |
| `detail_close` | Close without save |
| `lookup_select` | Confirm selection |
| `lookup_discard` | Cancel selection |

## Notifications and Dialogs
```java
@Autowired
private Notifications notifications;

@Autowired
private Dialogs dialogs;

// Notification
notifications.create("Customer saved")
        .withType(Notifications.Type.SUCCESS)
        .withPosition(Notification.Position.TOP_END)
        .show();

// Confirmation dialog
dialogs.createOptionDialog()
        .withHeader("Confirm")           // NOT withCaption()!
        .withText("Delete customer?")    // NOT withMessage()!
        .withActions(
            new DialogAction(DialogAction.Type.YES).withHandler(e -> delete()),
            new DialogAction(DialogAction.Type.NO)
        )
        .open();                          // NOT .show()!

// Input dialog
dialogs.createInputDialog(this)
        .withHeader("Enter Values")
        .withParameters(
            InputParameter.stringParameter("name").withLabel("Name").withRequired(true),
            InputParameter.entityParameter("customer", Customer.class).withLabel("Customer")
        )
        .withActions(DialogActions.OK_CANCEL)
        .withCloseListener(e -> {
            if (e.closedWith(DialogOutcome.OK)) {
                String name = e.getValue("name");
            }
        })
        .open();
```

### ⚠️ Dialogs from Fragments
```java
// From fragment — MUST pass parent View, not 'this'!
View<?> parentView = UiComponentUtils.getView(this);
dialogs.createInputDialog(parentView)
        .withHeader("...")
        .open();
```

## DataContext
Tracks entity changes in detail views:
```java
@ViewComponent
private DataContext dataContext;

// Check for changes
boolean hasChanges = dataContext.hasChanges();

// Get modified entities
Collection<Object> modified = dataContext.getModified();

// Merge external entity
Customer merged = dataContext.merge(externalCustomer);

// Discard changes
dataContext.clear();
```

## @Subscribe Targets

| Target | Description |
|--------|-------------|
| (default) | View lifecycle events |
| `Target.DATA_CONTEXT` | PreCommit, PostCommit, Change |
| `Target.DATA_LOADER` | PreLoad, PostLoad |
| `Target.DATA_CONTAINER` | ItemChange, ItemPropertyChange |

## Facets
```xml
<facets>
    <dataLoadCoordinator auto="true"/>
    <urlQueryParameters>
        <genericFilter component="genericFilter"/>
        <pagination component="pagination"/>
    </urlQueryParameters>
</facets>
```

## Fragments
### Fragment Controller
```java
@FragmentDescriptor("address-fragment.xml")  // No @ViewController!
public class AddressFragment extends Fragment<FormLayout> {
    
    @ViewComponent
    private InstanceContainer<Address> addressDc;
    
    // Subscribe to HOST view events (fragments have no BeforeShow!)
    @Subscribe(target = Target.HOST_CONTROLLER)
    public void onHostBeforeShow(View.BeforeShowEvent event) {
        // Load data when host view shows
    }
}
```

### Fragment XML
```xml
<fragment xmlns="http://jmix.io/schema/flowui/fragment">
    <data>
        <instance id="addressDc" class="Address" provided="true"/>
    </data>
    <content>
        <formLayout dataContainer="addressDc">
            <textField property="city"/>
            <textField property="street"/>
        </formLayout>
    </content>
</fragment>
```

### Using Fragment in View
```xml
<fragment id="addressFragment" 
          class="com.company.view.AddressFragment"/>

<!-- With properties -->
<fragment class="com.company.view.AddressFragment">
    <properties>
        <property name="addressDc" value="customerAddressDc" type="CONTAINER_REF"/>
    </properties>
</fragment>
```

### Provided Data Containers
Mark as `provided="true"` when data comes from host view:
```xml
<data>
    <instance id="customerDc" class="Customer" provided="true">
        <collection id="ordersDc" property="orders" provided="true"/>
    </instance>
</data>
```

## Layout Best Practices

### Always Control Padding
```xml
<!-- ❌ Will have extra padding -->
<vbox>
    <dataGrid/>
</vbox>

<!-- ✅ Explicit padding control -->
<vbox padding="false" spacing="true" margin="false">
    <dataGrid/>
</vbox>
```

### Buttons Panel
```xml
<!-- Buttons above DataGrid -->
<hbox classNames="buttons-panel">
    <button action="customersDataGrid.create"/>
    <button action="customersDataGrid.edit"/>
    <button action="customersDataGrid.remove"/>
</hbox>
<dataGrid id="customersDataGrid" .../>
```

### Sections with Headers → Details
```xml
<!-- Instead of groupBox, use details -->
<details opened="true" summaryText="Contact Info">
    <formLayout>
        <textField property="email"/>
        <textField property="phone"/>
    </formLayout>
</details>
```

### Form Layout
```xml
<formLayout width="100%" responsiveSteps="1, 2">
    <textField label="Name" property="name"/>
    <textField label="Email" property="email"/>
</formLayout>
```

## DataGrid Inline Editing
```xml
<dataGrid editorBuffered="true">
    <columns>
        <column property="name" editable="true"/>
        <column property="value" editable="true"/>
        <editorActionsColumn>
            <editButton icon="PENCIL"/>
            <saveButton icon="CHECK"/>
            <cancelButton icon="CLOSE"/>
        </editorActionsColumn>
    </columns>
</dataGrid>
```

## Action enabledRule with External Dependency
```java
// When rule depends on OTHER component, refresh manually
@Install(to = "ordersDataGrid.create", subject = "enabledRule")
private boolean createEnabledRule() {
    return customersDataGrid.getSelectedItems().size() == 1;
}

@Subscribe("customersDataGrid")
public void onCustomersSelection(SelectionEvent<...> event) {
    ordersDataGrid.getAction("create").refreshState();  // Re-evaluate rule
}
```

## CUBA → Jmix API Changes

| CUBA | Jmix |
|------|------|
| `@UiController` | `@ViewController` + `@Route` |
| `@Inject` (UI) | `@ViewComponent` |
| `@Inject` (beans) | `@Autowired` |
| `AfterShowEvent` | `ReadyEvent` |
| `StandardLookup` | `StandardListView` |
| `StandardEditor` | `StandardDetailView` |
| `GroupTable` | `DataGrid` |
| `LookupField` | `EntityComboBox` |
| `.withCaption()` | `.withHeader()` |
| `.withMessage()` | `.withText()` |
| `.show()` (dialogs) | `.open()` |
| `StandardCloseAction.COMMIT` | `StandardOutcome.SAVE` |
| `view="..."` (XML data) | `fetchPlan="..."` |
| `screen="..."` (fragment) | `class="..."` |
| `<buttonsPanel>` | `<hbox classNames="buttons-panel">` |
| `<groupBox caption>` | `<details summaryText>` |
| `<form>` | `<formLayout>` |
| `caption=` | `label=` |

## Forbidden
- Business logic in view controllers (move to services)
- EntityManager usage (use DataManager)
- Direct database transactions
