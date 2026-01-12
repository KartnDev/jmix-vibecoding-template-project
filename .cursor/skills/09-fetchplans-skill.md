# Skill: Fetch Plans

## Description
Configuring what data to load from database to avoid N+1 queries and control lazy loading.

## Imports
```java
import io.jmix.core.FetchPlan;
import io.jmix.core.FetchPlans;
import io.jmix.core.FetchPlanRepository;
import io.jmix.core.FetchMode;
```

## Built-in Fetch Plans

| Name | Constant | Description |
|------|----------|-------------|
| `_local` | `FetchPlan.LOCAL` | All local attributes (non-references) |
| `_instance_name` | `FetchPlan.INSTANCE_NAME` | Attributes forming instance name (can include references). Empty if no `@InstanceName` defined |
| `_base` | `FetchPlan.BASE` | `_local` + `_instance_name` + embedded references |

**Best Practice:** Use `_base` by default until you face performance issues with large lists.

## Fetch Plan in XML (Repository)
Create `fetch-plans.xml` in `src/main/resources/.../`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<fetch-plans xmlns="http://jmix.io/schema/core/fetch-plans">

    <fetch-plan entity="Order" name="order-with-customer" extends="_base">
        <property name="customer" fetchPlan="_base"/>
    </fetch-plan>

    <fetch-plan entity="Order" name="order-with-lines" extends="_base">
        <property name="customer" fetchPlan="_instance_name"/>
        <property name="lines" fetchPlan="_base">
            <property name="product" fetchPlan="_base"/>
        </property>
    </fetch-plan>

    <fetch-plan entity="Customer" name="customer-full" extends="_base">
        <property name="orders" fetchPlan="_base"/>
        <property name="contacts" fetchPlan="_base"/>
    </fetch-plan>

</fetch-plans>
```

## Using Named Fetch Plan
```java
// In service
Order order = dataManager.load(Order.class)
        .id(orderId)
        .fetchPlan("order-with-lines")
        .one();

// In view XML
<instance id="orderDc" class="com.company.project.entity.Order">
    <fetchPlan extends="_base">
        <property name="customer" fetchPlan="_instance_name"/>
        <property name="lines" fetchPlan="_base"/>
    </fetchPlan>
    <loader/>
</instance>
```

## Inline Fetch Plan (Programmatic)
```java
Order order = dataManager.load(Order.class)
        .id(orderId)
        .fetchPlan(fpBuilder -> fpBuilder
                .addAll("number", "date", "status", "total")
                .add("customer", builder -> builder.addAll("name", "email"))
                .add("lines", builder -> builder
                        .addAll("quantity", "price")
                        .add("product", pBuilder -> pBuilder.add("name"))
                )
        )
        .one();
```

## FetchPlans Bean (Builder)
Use `FetchPlans` to create fetch plans programmatically:
```java
private final FetchPlans fetchPlans;

public OrderService(FetchPlans fetchPlans) {
    this.fetchPlans = fetchPlans;
}

public FetchPlan getOrderWithLinesPlan() {
    return fetchPlans.builder(Order.class)
            .addFetchPlan(FetchPlan.BASE)
            .add("customer", builder -> builder.addFetchPlan(FetchPlan.INSTANCE_NAME))
            .add("lines", builder -> builder
                    .addFetchPlan(FetchPlan.BASE)
                    .add("product", pBuilder -> pBuilder.addFetchPlan(FetchPlan.BASE))
            )
            .build();
}
```

## FetchPlanRepository (Named Plans)
Use `FetchPlanRepository` to retrieve named fetch plans from XML:
```java
private final FetchPlanRepository fetchPlanRepository;

public OrderService(FetchPlanRepository fetchPlanRepository) {
    this.fetchPlanRepository = fetchPlanRepository;
}

public Order loadOrder(UUID orderId) {
    FetchPlan plan = fetchPlanRepository.getFetchPlan(Order.class, "order-with-lines");
    return dataManager.load(Order.class)
            .id(orderId)
            .fetchPlan(plan)
            .one();
}
```

## Fetch Plan in View Descriptor
```xml
<data>
    <collection id="ordersDc" class="com.company.project.entity.Order">
        <fetchPlan extends="_base">
            <property name="customer" fetchPlan="_instance_name"/>
        </fetchPlan>
        <loader id="ordersDl">
            <query>
                <![CDATA[select o from Order o order by o.date desc]]>
            </query>
        </loader>
    </collection>
</data>
```

## Lazy vs Eager Loading

### Problem: N+1 Queries
```java
// BAD: N+1 problem
List<Order> orders = dataManager.load(Order.class).all().list();
for (Order order : orders) {
    // Each call triggers separate query!
    System.out.println(order.getCustomer().getName());
}
```

### Solution: Fetch Plan
```java
// GOOD: Single query with JOIN
List<Order> orders = dataManager.load(Order.class)
        .all()
        .fetchPlan(fp -> fp.add("customer"))
        .list();
for (Order order : orders) {
    // Already loaded, no extra query
    System.out.println(order.getCustomer().getName());
}
```

## Fetch Modes

| Mode | SQL Behavior | Best For |
|------|--------------|----------|
| `AUTO` | Framework decides (default) | General use |
| `JOIN` | Single query with SQL JOIN | To-one relations |
| `BATCH` | Separate batched query | Collections (avoids Cartesian product) |

### XML Example
```xml
<fetch-plan entity="Order" name="order-optimized" extends="_base">
    <property name="customer" fetchPlan="_instance_name" fetchMode="JOIN"/>
    <property name="lines" fetchPlan="_base" fetchMode="BATCH"/>
</fetch-plan>
```

### Programmatic Example
```java
FetchPlan plan = fetchPlans.builder(Order.class)
        .addFetchPlan(FetchPlan.BASE)
        .add("customer", FetchMode.JOIN)
        .add("lines", builder -> builder.addFetchPlan(FetchPlan.BASE), FetchMode.BATCH)
        .build();
```

## Common Patterns

### List View (Minimal)
```xml
<fetchPlan extends="_base">
    <property name="customer" fetchPlan="_instance_name"/>
    <property name="status"/>
</fetchPlan>
```

### Detail View (Full)
```xml
<fetchPlan extends="_base">
    <property name="customer" fetchPlan="_base"/>
    <property name="lines" fetchPlan="_base">
        <property name="product" fetchPlan="_base"/>
    </property>
</fetchPlan>
```

## List View Considerations

List views typically show 3-6 columns from the same entity — often `_base` is enough without any JOINs.

Think before adding nested collections:
```xml
<!-- Overkill for a simple table -->
<fetchPlan extends="_base">
    <property name="lines" fetchPlan="_base">
        <property name="product" fetchPlan="_base">
            <property name="categories" fetchPlan="_base"/>  <!-- Really needed in table? -->
        </property>
    </property>
</fetchPlan>

<!-- Usually enough -->
<fetchPlan extends="_base">
    <property name="customer" fetchPlan="_instance_name"/>
</fetchPlan>
```

## Virtual Lists / Infinite Scroll

When using virtual lists with lazy loading — **think carefully**:
- Loading list of lists is OK, but plan buffering strategy
- Consider pagination vs infinite scroll trade-offs
- Pre-calculate what to load on scroll, don't load everything
- Without proper lazy loading strategy — app will crash on large datasets

```java
// Consider batch size and buffer for virtual scrolling
dataManager.load(Order.class)
        .query("select o from Order o order by o.date desc")
        .firstResult(offset)
        .maxResults(batchSize)  // Load in chunks
        .fetchPlan(fp -> fp.addFetchPlan(FetchPlan.BASE))
        .list();
```

### Report (Custom)
```java
.fetchPlan(fp -> fp
    .addAll("number", "date", "total")
    .add("customer", c -> c.addAll("name", "email", "phone"))
)
```

## JmixDataRepository
Fetch plan as last parameter:
```java
public interface OrderRepository extends JmixDataRepository<Order, UUID> {
    List<Order> findByStatus(OrderStatus status, FetchPlan fetchPlan);
    
    @Query("select o from Order o where o.customer = :customer")
    List<Order> findByCustomer(@Param("customer") Customer customer, FetchPlan fetchPlan);
}
```

## Partial Entity Loading
Loading entity without `_base` — only selected attributes:
```java
FetchPlan partialPlan = fetchPlans.builder(User.class)
        .add("email")
        .build();

User user = dataManager.load(User.class).id(userId).fetchPlan(partialPlan).one();
String email = user.getEmail();       // OK
String name = user.getFirstName();    // THROWS IllegalStateException!
```
**Caveat:** Accessing unfetched attribute throws `IllegalStateException`.

## Soft Delete Interaction
Fetch plans control columns, soft delete controls rows:
```java
// Standard: soft-deleted references will be null
Order order = dataManager.load(Order.class).id(id).one();

// Include deleted references
import io.jmix.core.PersistenceHints;

Order order = dataManager.load(Order.class)
        .id(id)
        .hint(PersistenceHints.SOFT_DELETION, false)
        .fetchPlan(fp -> fp.addFetchPlan(FetchPlan.BASE).add("customer"))
        .one();
```

## UnconstrainedDataManager
Bypasses row-level security but respects fetch plans:
```java
private final UnconstrainedDataManager unconstrainedDataManager;

// For system/admin operations
Order order = unconstrainedDataManager.load(Order.class)
        .id(orderId)
        .fetchPlan("order-with-lines")
        .one();
```

## Checklist
- [ ] Define fetch plans for complex entities
- [ ] Use `_instance_name` for references in lists
- [ ] Use `_base` or custom for detail views
- [ ] Check for N+1 queries in logs
- [ ] Named fetch plans in `fetch-plans.xml`

## Entity Best Practice
Always use `LAZY` on entities, control loading via fetch plans:
```java
@ManyToOne(fetch = FetchType.LAZY)  // Always LAZY
@JoinColumn(name = "CUSTOMER_ID")
private Customer customer;
```

## Forbidden
- `FetchType.EAGER` on entity relationships
- Loading without fetch plan (N+1 problem)
- Deep nesting (>3 levels) without reason
- Using fetch plans as security mechanism
- Field injection (`@Autowired` on fields)
