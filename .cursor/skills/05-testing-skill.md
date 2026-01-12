# Skill: Testing

## Description
Writing unit tests and UI tests for Jmix applications.

## Unit Test Template
```java
package com.company.project.service;

import com.company.project.entity.Customer;
import io.jmix.core.DataManager;
import io.jmix.core.security.SystemAuthenticator;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
class CustomerServiceTest {

    @Autowired
    DataManager dataManager;

    @Autowired
    SystemAuthenticator systemAuthenticator;

    @Autowired
    CustomerService customerService;

    @Test
    void shouldCreateCustomer() {
        systemAuthenticator.runWithSystem(() -> {
            Customer customer = customerService.createCustomer("Test Customer");

            assertThat(customer.getId()).isNotNull();
            assertThat(customer.getName()).isEqualTo("Test Customer");

            // Cleanup
            dataManager.remove(customer);
        });
    }

    @Test
    void shouldFindCustomerByEmail() {
        systemAuthenticator.runWithSystem(() -> {
            // Setup
            Customer customer = dataManager.create(Customer.class);
            customer.setName("John");
            customer.setEmail("john@example.com");
            dataManager.save(customer);

            // Test
            Customer found = customerService.findByEmail("john@example.com");

            assertThat(found).isNotNull();
            assertThat(found.getName()).isEqualTo("John");

            // Cleanup
            dataManager.remove(customer);
        });
    }
}
```

## Authenticated Test Annotation
```java
package com.company.project.test_support;

import org.springframework.security.test.context.support.WithUserDetails;

import java.lang.annotation.*;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@WithUserDetails("admin")
public @interface AuthenticatedAsAdmin {
}
```

Usage:
```java
@SpringBootTest
class CustomerServiceTest {

    @Test
    @AuthenticatedAsAdmin
    void shouldLoadCustomersAsAdmin() {
        List<Customer> customers = dataManager.load(Customer.class).all().list();
        assertThat(customers).isNotEmpty();
    }
}
```

## UI Test with Test Assist (Jmix Add-on)
```java
package com.company.project.view.customer;

import com.company.project.entity.Customer;
import com.company.project.test_support.AuthenticatedAsAdmin;
import io.jmix.flowui.ViewNavigators;
import io.jmix.flowui.testassist.FlowuiTestAssistConfiguration;
import io.jmix.flowui.testassist.UiTest;
import io.jmix.flowui.testassist.UiTestUtils;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.assertj.core.api.Assertions.assertThat;

@UiTest
@SpringBootTest(classes = {FlowuiTestAssistConfiguration.class})
class CustomerListViewTest {

    @Autowired
    ViewNavigators viewNavigators;

    @Test
    @AuthenticatedAsAdmin
    void shouldOpenListView() {
        viewNavigators.view(CustomerListView.class).navigate();

        CustomerListView view = UiTestUtils.getCurrentView();

        assertThat(view).isNotNull();
    }

    @Test
    @AuthenticatedAsAdmin
    void shouldDisplayCustomersInGrid() {
        viewNavigators.view(CustomerListView.class).navigate();

        CustomerListView view = UiTestUtils.getCurrentView();
        var dataGrid = UiTestUtils.getComponent(view, "customersDataGrid");

        assertThat(dataGrid).isNotNull();
    }
}
```

## DataManager in Tests
```java
@Test
void shouldTestWithDataManager() {
    systemAuthenticator.runWithSystem(() -> {
        // Create test data
        Customer customer = dataManager.create(Customer.class);
        customer.setName("Test");
        customer = dataManager.save(customer);

        // Load with fetch plan
        Customer loaded = dataManager.load(Customer.class)
                .id(customer.getId())
                .fetchPlan("customer-with-orders")
                .one();

        // Assert
        assertThat(loaded.getOrders()).isEmpty();

        // Cleanup
        dataManager.remove(customer);
    });
}
```

## Test Configuration
```java
@SpringBootTest
@TestPropertySource(properties = {
    "jmix.core.available-locales=en",
    "spring.datasource.url=jdbc:hsqldb:mem:testdb"
})
class MyTest {
    // ...
}
```

## Test Dependencies (build.gradle)
```groovy
testImplementation 'org.springframework.boot:spring-boot-starter-test'
testImplementation 'io.jmix.flowui:jmix-flowui-test-assist'
testImplementation 'org.springframework.security:spring-security-test'
```

## Checklist
- [ ] Use `@SpringBootTest`
- [ ] Use `SystemAuthenticator` for system-level operations
- [ ] Use `@AuthenticatedAsAdmin` or `@WithUserDetails` for user context
- [ ] Clean up test data after tests
- [ ] Use `@UiTest` for UI tests

## Forbidden
- Direct database queries bypassing DataManager
- Tests without proper authentication context
- Leaving test data in database
