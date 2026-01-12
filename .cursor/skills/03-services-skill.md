# Skill: Services

## Description
Creating business logic services with proper Jmix patterns.

## Service Template
```java
package com.company.project.service;

import com.company.project.entity.Customer;
import com.company.project.entity.Order;
import io.jmix.core.DataManager;
import io.jmix.core.FetchPlan;
import io.jmix.core.FetchPlans;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.UUID;

@Service
public class OrderService {

    private final DataManager dataManager;
    private final FetchPlans fetchPlans;

    // Constructor injection (NO @Autowired on fields!)
    public OrderService(DataManager dataManager, FetchPlans fetchPlans) {
        this.dataManager = dataManager;
        this.fetchPlans = fetchPlans;
    }

    public Order createOrder(Customer customer) {
        Order order = dataManager.create(Order.class);
        order.setCustomer(customer);
        order.setStatus(OrderStatus.NEW);
        return dataManager.save(order);
    }

    public List<Order> findByCustomer(Customer customer) {
        return dataManager.load(Order.class)
                .query("select o from Order o where o.customer = :customer")
                .parameter("customer", customer)
                .list();
    }

    public Order findById(UUID id) {
        return dataManager.load(Order.class)
                .id(id)
                .fetchPlan("order-with-lines")
                .one();
    }

    @Transactional
    public void processOrder(Order order) {
        // Business logic with transaction
        order.setStatus(OrderStatus.PROCESSING);
        dataManager.save(order);
    }
}
```

## DataManager Operations

### Create
```java
Customer customer = dataManager.create(Customer.class);
customer.setName("John");
dataManager.save(customer);
```

### Load by ID
```java
Customer customer = dataManager.load(Customer.class)
        .id(customerId)
        .one();

// Optional result
Optional<Customer> optional = dataManager.load(Customer.class)
        .id(customerId)
        .optional();
```

### Load List
```java
List<Customer> customers = dataManager.load(Customer.class)
        .query("select c from Customer c where c.active = true")
        .list();
```

### Load with Parameters
```java
List<Order> orders = dataManager.load(Order.class)
        .query("select o from Order o where o.customer = :customer and o.status = :status")
        .parameter("customer", customer)
        .parameter("status", OrderStatus.NEW)
        .list();
```

### Load with Fetch Plan
```java
Order order = dataManager.load(Order.class)
        .id(orderId)
        .fetchPlan("order-with-lines")
        .one();

// Or inline fetch plan
Order order = dataManager.load(Order.class)
        .id(orderId)
        .fetchPlan(fpBuilder -> fpBuilder.addAll(
                "number", "date", "customer.name", "lines.product.name"
        ))
        .one();
```

### Save Multiple
```java
dataManager.save(order, customer, lines.toArray(new OrderLine[0]));

// Or with SaveContext
SaveContext saveContext = new SaveContext();
saveContext.saving(order);
saveContext.saving(customer);
saveContext.removing(deletedLines);
dataManager.save(saveContext);
```

### Remove
```java
dataManager.remove(entity);

// Or by ID
dataManager.remove(Id.of(Customer.class, customerId));
```

## Transactions
```java
@Transactional
public void complexOperation() {
    // Multiple operations in one transaction
    Order order = dataManager.save(newOrder);
    for (OrderLine line : lines) {
        line.setOrder(order);
        dataManager.save(line);
    }
}

@Transactional(readOnly = true)
public List<Order> getOrdersReport() {
    // Read-only transaction
    return dataManager.load(Order.class).all().list();
}
```

## Checklist
- [ ] `@Service` annotation
- [ ] Constructor injection for dependencies
- [ ] Use `DataManager` for data access
- [ ] Use `@Transactional` for write operations
- [ ] Use fetch plans for eager loading

## Forbidden
- Field `@Autowired` (use constructor injection)
- `EntityManager` (use `DataManager`)
- Business logic in views
- Direct JDBC (use DataManager)
