# Skill: Fetch Plans

## Description
Configuring what data to load from database to avoid N+1 queries and control lazy loading.

## Built-in Fetch Plans

| Name | Description |
|------|-------------|
| `_base` | All local (non-reference) attributes |
| `_instance_name` | Only `@InstanceName` attribute |
| `_local` | Same as `_base` |

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

## FetchPlan Builder
```java
@Autowired
private FetchPlans fetchPlans;

public FetchPlan getOrderWithLinesPlan() {
    return fetchPlans.builder(Order.class)
            .addFetchPlan("_base")
            .add("customer", builder -> builder.addFetchPlan("_instance_name"))
            .add("lines", builder -> builder
                    .addFetchPlan("_base")
                    .add("product", pBuilder -> pBuilder.addFetchPlan("_base"))
            )
            .build();
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

### Report (Custom)
```java
.fetchPlan(fp -> fp
    .addAll("number", "date", "total")
    .add("customer", c -> c.addAll("name", "email", "phone"))
)
```

## Checklist
- [ ] Define fetch plans for complex entities
- [ ] Use `_instance_name` for references in lists
- [ ] Use `_base` or custom for detail views
- [ ] Check for N+1 queries in logs
- [ ] Named fetch plans in `fetch-plans.xml`

## Forbidden
- Loading all attributes when only few needed
- Ignoring lazy loading issues
- Deep nesting without limits (performance)
