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

## Component Injection
```java
@ViewComponent
private DataGrid<Customer> customersDataGrid;

@ViewComponent
private CollectionLoader<Customer> customersDl;
```

## Event Handlers
```java
@Subscribe
public void onInit(InitEvent event) {
    // Initialize view
}

@Subscribe
public void onBeforeShow(BeforeShowEvent event) {
    // Before view is shown
}

@Subscribe("customersDataGrid.create")
public void onCreateAction(ActionPerformedEvent event) {
    // Custom create logic
}

@Install(to = "customersDl", target = Target.DATA_LOADER)
private List<Customer> customersDlLoadDelegate(LoadContext<Customer> loadContext) {
    // Custom loading logic
    return dataManager.loadList(loadContext);
}
```

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
