# Skill: Testing

## Description
Writing reliable tests for Jmix 2.x applications at different levels: Unit, Layered (Integration), and E2E.

## Test Pyramid: 3 Levels

### 1. Unit Tests (No Spring Context)
Pure unit tests for isolated logic. No `@SpringBootTest`, fast execution.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.assertj.core.api.Assertions.assertThat;

class PriceCalculatorTest {

    private final PriceCalculator calculator = new PriceCalculator();

    @ParameterizedTest
    @CsvSource({
        "100, 10, 90",   // 10% discount
        "100, 0, 100",   // no discount
        "50, 50, 25"     // 50% discount
    })
    void shouldApplyDiscount(int price, int discount, int expected) {
        assertThat(calculator.applyDiscount(price, discount)).isEqualTo(expected);
    }
}
```

### 2. Layered Tests (Integration with Mocks)
Test specific layers with `@SpringBootTest` + mocked dependencies.
**Note:** `@MockBean` is deprecated since Spring Boot 3.4+ / Java 17+. Use `@MockitoBean` instead.

```java
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.bean.override.mockito.MockitoBean;

import static org.mockito.Mockito.*;

@SpringBootTest
class OrderServiceLayeredTest {

    @Autowired
    OrderService orderService;

    @MockitoBean // NOT @MockBean (deprecated)
    PaymentGateway paymentGateway;

    @Test
    void shouldProcessPayment() {
        // Arrange
        when(paymentGateway.charge(any())).thenReturn(true);

        // Act
        orderService.checkout(order);

        // Assert
        verify(paymentGateway).charge(any());
    }
}
```

### 3. E2E Tests (Full Stack)
Full application with real DB (TestContainers). **Do NOT inject beans directly** — breaks thread model and transactions.

```java
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.server.LocalServerPort;
import org.springframework.test.context.DynamicPropertyRegistry;
import org.springframework.test.context.DynamicPropertySource;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;
import io.restassured.RestAssured;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@Testcontainers
class OrderApiE2ETest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");

    @LocalServerPort
    int port;

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("main.datasource.url", postgres::getJdbcUrl);
        registry.add("main.datasource.username", postgres::getUsername);
        registry.add("main.datasource.password", postgres::getPassword);
    }

    @Test
    void shouldCreateOrderViaRest() {
        // ❌ DO NOT: @Autowired OrderService — breaks isolation!
        // ✅ DO: Use HTTP client (RestAssured, WebTestClient)
        
        RestAssured.given()
            .port(port)
            .contentType("application/json")
            .body("{\"customerId\": \"...\"}")
        .when()
            .post("/api/orders")
        .then()
            .statusCode(201);
    }
}
```

**E2E Database Cleanup Options:**
- Re-drop schema via Liquibase before each test class.
- Use `@Sql` scripts for cleanup.
- Create a JUnit Extension for DB reset.

### 4. UI E2E Tests (Browser-Based)
⚠️ **Advanced Topic** — requires careful setup, consider if you really need it.

**Requirements:**
- Full application running (e.g., via `@SpringBootTest(webEnvironment = RANDOM_PORT)` or separate process)
- Selenium WebDriver (ideally in a container: `selenium/standalone-chrome`)
- Jmix addon: `io.jmix.masquerade:jmix-masquerade` (wrapper over Selenide)

**Key Points:**
- This is a **slippery topic** — many moving parts (app lifecycle, browser, network, timing).
- You're testing the **real browser** against a **real running app**.
- Flaky tests are common; invest in proper waits and retry logic.
- Consider if `flowui-test-assist` (in-memory, no browser) covers your needs first.

**Resources:**
- [Jmix Masquerade Docs](https://docs.jmix.io/jmix/testing/masquerade.html)
- [Selenide](https://selenide.org/)

> 💡 **Recommendation:** Start with `flowui-test-assist` for most UI tests. Use browser E2E only for critical smoke tests.

---

## Key Principles (for Layered/Integration Tests)
1. **NO `@Transactional` on tests** — Jmix's `DataManager` manages its own transactions. Test-level `@Transactional` causes lazy loading issues and false positives.
2. **Manual Cleanup in `@AfterEach`** — Always clean up in lifecycle methods, never at the end of test methods (assertions may fail and skip cleanup).
3. **Use `SystemAuthenticator`** — Preferred over `@WithUserDetails` for consistent Jmix security context.

## Service Integration Test Template
```java
package com.company.project.service;

import com.company.project.entity.Customer;
import io.jmix.core.DataManager;
import io.jmix.core.security.SystemAuthenticator;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.ArrayList;
import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
class CustomerServiceTest {

    @Autowired
    DataManager dataManager;

    @Autowired
    SystemAuthenticator systemAuthenticator;

    @Autowired
    CustomerService customerService;

    private final List<Object> testEntities = new ArrayList<>();

    @BeforeEach
    void setUp() {
        systemAuthenticator.begin("admin");
    }

    @AfterEach
    void tearDown() {
        try {
            // Clean up ALL created entities
            testEntities.forEach(dataManager::remove);
            testEntities.clear();
        } finally {
            systemAuthenticator.end();
        }
    }

    @Test
    void shouldCreateCustomer() {
        // Given
        Customer customer = customerService.createCustomer("Test");

        // Track for cleanup
        testEntities.add(customer);

        // Then
        assertThat(customer.getId()).isNotNull();
        assertThat(customer.getName()).isEqualTo("Test");
    }
}
```

## JUnit Extension for Authentication (Recommended)
Create a reusable extension instead of repeating `begin()/end()` in every test class.

```java
package com.company.project.test_support;

import io.jmix.core.security.SystemAuthenticator;
import org.junit.jupiter.api.extension.*;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.junit.jupiter.SpringExtension;

public class AuthenticatedAsAdmin implements BeforeEachCallback, AfterEachCallback {

    @Override
    public void beforeEach(ExtensionContext context) {
        getAuthenticator(context).begin("admin");
    }

    @Override
    public void afterEach(ExtensionContext context) {
        getAuthenticator(context).end();
    }

    private SystemAuthenticator getAuthenticator(ExtensionContext context) {
        ApplicationContext appCtx = SpringExtension.getApplicationContext(context);
        return appCtx.getBean(SystemAuthenticator.class);
    }
}
```

**Usage:**
```java
@SpringBootTest
@ExtendWith(AuthenticatedAsAdmin.class)
class MyServiceTest {
    // No need for @BeforeEach/@AfterEach for auth
}
```

## UI Test Template
Uses `jmix-flowui-test-assist`. **Must include main application class.**

```java
package com.company.project.view.user;

import com.company.project.JmixCommonProjectApplication;
import io.jmix.flowui.testassist.FlowuiTestAssistConfiguration;
import io.jmix.flowui.testassist.UiTest;
import io.jmix.flowui.testassist.UiTestUtils;
import io.jmix.flowui.ViewNavigators;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.assertj.core.api.Assertions.assertThat;

@UiTest
@SpringBootTest(classes = {JmixCommonProjectApplication.class, FlowuiTestAssistConfiguration.class})
@ExtendWith(AuthenticatedAsAdmin.class)
class UserListViewTest {

    @Autowired
    ViewNavigators viewNavigators;

    @Test
    void shouldOpenListView() {
        viewNavigators.view(UserListView.class).navigate();

        UserListView view = UiTestUtils.getCurrentView();

        assertThat(view).isNotNull();
    }
}
```

## Test Data Builders (Optional but Recommended)
Avoid repetitive entity creation. Use builders that persist via `DataManager`.

```java
package com.company.project.test_support;

import com.company.project.entity.User;
import io.jmix.core.DataManager;

public class UserFixture {
    private final DataManager dataManager;

    public UserFixture(DataManager dataManager) {
        this.dataManager = dataManager;
    }

    public UserBuilder user() {
        return new UserBuilder();
    }

    public class UserBuilder {
        private String username = "user_" + System.nanoTime();
        private Boolean active = true;

        public UserBuilder username(String v) { this.username = v; return this; }
        public UserBuilder active(Boolean v) { this.active = v; return this; }

        public User save() {
            User u = dataManager.create(User.class);
            u.setUsername(username);
            u.setActive(active);
            return dataManager.save(u);
        }
    }
}
```

## Dependencies (build.gradle)
```groovy
testImplementation 'org.springframework.boot:spring-boot-starter-test'
testImplementation 'io.jmix.flowui:jmix-flowui-test-assist'
```

## Checklist
- [ ] **Choose correct level:** Unit → Layered → E2E
- [ ] **NO `@Transactional`** on integration test methods
- [ ] **Cleanup in `@AfterEach`** (not at end of test)
- [ ] **Use `@MockitoBean`** (NOT deprecated `@MockBean`)
- [ ] **Use `SystemAuthenticator`** for Jmix security context
- [ ] **E2E:** Do NOT inject beans; use HTTP clients only
- [ ] **Assertions:** Use AssertJ (`assertThat`)

## Forbidden
- `@Transactional` on test classes/methods (breaks Jmix DataManager)
- `@MockBean` (deprecated since Spring Boot 3.4+, use `@MockitoBean`)
- `@WithUserDetails` (use `SystemAuthenticator` instead)
- `@Autowired` beans in E2E tests (breaks thread/transaction model)
- Cleanup at end of test method (use `@AfterEach`)
- Hardcoded UUIDs
- `System.out.println` (use Logger)