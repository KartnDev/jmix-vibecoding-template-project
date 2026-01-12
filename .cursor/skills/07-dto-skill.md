# Skill: DTOs (Data Transfer Objects)

## Description
Creating DTOs for API responses, reports, and non-persistent data structures.

## When to Use What

| Use Case | Pattern |
|----------|---------|
| Jmix UI (dataGrid, forms) | `@JmixEntity` + `@JmixId` |
| REST API request/response | Plain POJO or Java Record |
| Internal service layer | Plain POJO or Java Record |

## Java Records (Recommended for REST/immutable DTOs)
```java
// Simple, immutable, no boilerplate
public record OrderRequest(UUID customerId, List<LineItem> items) {}

public record CustomerSummary(String name, int orderCount, BigDecimal total) {}
```

## DTO Template
```java
package com.company.project.dto;

import io.jmix.core.entity.annotation.JmixGeneratedValue;
import io.jmix.core.entity.annotation.JmixId;
import io.jmix.core.metamodel.annotation.JmixEntity;
import io.jmix.core.metamodel.annotation.InstanceName;

import java.math.BigDecimal;
import java.util.UUID;

@JmixEntity(name = "CustomerSummaryDto")
public class CustomerSummaryDto {

    @JmixId
    @JmixGeneratedValue
    private UUID id;

    @InstanceName
    private String customerName;

    private Integer orderCount;
    
    private BigDecimal totalAmount;

    // Getters and setters
    public UUID getId() { return id; }
    public void setId(UUID id) { this.id = id; }

    public String getCustomerName() { return customerName; }
    public void setCustomerName(String customerName) { this.customerName = customerName; }

    public Integer getOrderCount() { return orderCount; }
    public void setOrderCount(Integer orderCount) { this.orderCount = orderCount; }

    public BigDecimal getTotalAmount() { return totalAmount; }
    public void setTotalAmount(BigDecimal totalAmount) { this.totalAmount = totalAmount; }
}
```

## Key Annotations

| Annotation | Purpose |
|------------|---------|
| `@JmixEntity` | Registers DTO in Jmix metadata (required for views) |
| `@JmixId` | Marks ID field (NOT `@Id` — that's for JPA entities!) |
| `@JmixGeneratedValue` | Auto-generates UUID |
| `@InstanceName` | Display name in UI |
| `@JmixProperty(mandatory=true)` | Required transient field (replaces CUBA's @MetaProperty) |

**Note:** `@JmixProperty` is redundant without parameters (defaults to `mandatory=false`).

## DTO vs Entity

| Aspect | Entity | DTO |
|--------|--------|-----|
| Persistence | `@Entity` + `@Table` | No JPA annotations |
| ID | UUID, `@Id` | `@JmixId` (any type) |
| Storage | Database | Memory only |
| Use case | CRUD | Reports, API, aggregations |

## Creating DTOs from Query
```java
@Service
public class ReportService {

    private final DataManager dataManager;

    public ReportService(DataManager dataManager) {
        this.dataManager = dataManager;
    }

    public List<CustomerSummaryDto> getCustomerSummary() {
        return dataManager.loadValues(
                "select c.name, count(o), sum(o.total) " +
                "from Customer c left join Order o on o.customer = c " +
                "group by c.name")
            .properties("customerName", "orderCount", "totalAmount")
            .list()
            .stream()
            .map(kv -> {
                CustomerSummaryDto dto = dataManager.create(CustomerSummaryDto.class);
                dto.setCustomerName(kv.getValue("customerName"));
                dto.setOrderCount(((Number) kv.getValue("orderCount")).intValue());
                dto.setTotalAmount((BigDecimal) kv.getValue("totalAmount"));
                return dto;
            })
            .toList();
    }
}
```

## DTO for REST API
```java
@JmixEntity(name = "OrderRequest")
public class OrderRequest {

    @JmixId
    @JmixGeneratedValue
    private UUID id;

    private UUID customerId;
    private List<OrderLineRequest> lines;

    // Nested DTO
    public static class OrderLineRequest {
        private UUID productId;
        private Integer quantity;
        // getters/setters
    }

    // getters/setters
}
```

## DTO View (List)
```java
@Route(value = "customer-summary", layout = MainView.class)
@ViewController("CustomerSummary.list")
@ViewDescriptor("customer-summary-list-view.xml")
public class CustomerSummaryListView extends StandardView {

    @Autowired
    private ReportService reportService;

    @ViewComponent
    private CollectionContainer<CustomerSummaryDto> summaryDc;

    @Subscribe
    public void onBeforeShow(BeforeShowEvent event) {
        summaryDc.setItems(reportService.getCustomerSummary());
    }
}
```

```xml
<view ...>
    <data>
        <collection id="summaryDc" class="com.company.project.dto.CustomerSummaryDto"/>
    </data>
    <layout>
        <dataGrid id="summaryDataGrid" dataContainer="summaryDc">
            <columns>
                <column property="customerName"/>
                <column property="orderCount"/>
                <column property="totalAmount"/>
            </columns>
        </dataGrid>
    </layout>
</view>
```

## Messages
Add to `messages_en.properties`:
```properties
com.company.project.dto/CustomerSummaryDto=Customer Summary
com.company.project.dto/CustomerSummaryDto.customerName=Customer
com.company.project.dto/CustomerSummaryDto.orderCount=Orders
com.company.project.dto/CustomerSummaryDto.totalAmount=Total
```

## Checklist
- [ ] `@JmixEntity` with unique name
- [ ] `@JmixId` on ID field
- [ ] `@InstanceName` on display field
- [ ] No JPA annotations (`@Entity`, `@Table`)
- [ ] Messages added for UI
- [ ] Use `dataManager.create()` to instantiate

## Validation (Bean Validation / Jakarta Validation)
Jmix fully supports standard Jakarta Bean Validation annotations.

```java
import jakarta.validation.constraints.*;

@JmixEntity(name = "app_OrderRequestDto")
public class OrderRequestDto {

    @JmixId
    @JmixGeneratedValue
    private UUID id;

    @NotNull(message = "Customer ID is required")
    private UUID customerId;

    @NotBlank(message = "Order number cannot be blank")
    @Size(min = 3, max = 50)
    private String orderNumber;

    @Valid  // Validates nested objects
    @NotEmpty(message = "At least one item required")
    private List<OrderLineDto> lines;

    @Positive
    private BigDecimal amount;

    @Email
    private String contactEmail;
}
```

**Supported annotations:** `@NotNull`, `@NotBlank`, `@NotEmpty`, `@Size`, `@Min`, `@Max`, `@Positive`, `@Email`, `@Pattern`, `@Past`, `@Future`, `@Valid`

## Validating DTO in Service

### Option A: Inject Validator
```java
@Service
public class OrderService {

    private final Validator validator;

    public OrderService(Validator validator) {
        this.validator = validator;
    }

    public void process(OrderRequestDto dto) {
        Set<ConstraintViolation<OrderRequestDto>> violations = validator.validate(dto);
        if (!violations.isEmpty()) {
            throw new ConstraintViolationException(violations);
        }
        // proceed...
    }
}
```

### Option B: @Validated on Service
```java
@Service
@Validated
public class OrderService {

    public void process(@Valid OrderRequestDto dto) {
        // Validation automatic, throws ConstraintViolationException
    }
}
```

## Lombok
- **Allowed on DTOs** (unlike entities where it's forbidden)
- Prefer Java Records for simple immutable DTOs
- Use Lombok only if you need mutable DTOs with builders

## Forbidden
- `@Entity` / `@Table` on DTOs
- Persisting DTOs to database
- Using `new Dto()` for UI DTOs (use `dataManager.create()`)
- Missing `@JmixId` on UI DTOs
