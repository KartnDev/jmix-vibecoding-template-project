# Skill: Services

## Description
Creating business logic services with proper Jmix patterns.

## Service Template

### Recommended: Constructor Injection
Best for testability and immutability. Standard in Spring Boot.

```java
package com.company.project.service;

import com.company.project.entity.Order;
import io.jmix.core.DataManager;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class OrderService {

    private final DataManager dataManager;

    // Recommended: Constructor injection
    public OrderService(DataManager dataManager) {
        this.dataManager = dataManager;
    }

    public Order createOrder() {
        return dataManager.create(Order.class);
    }
}
```

### Allowed: Field Injection (CUBA Style)
Field injection is **not forbidden** in Jmix 2.x. It is familiar to developers migrating from CUBA Platform, though less optimal for unit testing.

```java
@Service
public class LegacyStyleService {

    @Autowired
    private DataManager dataManager;

    // Valid Jmix code
}
```

## DataManager Operations (Primary API)
Always prefer `DataManager` over `EntityManager` for standard CRUD. It handles:
- Row-level security
- Entity listeners
- Partial loading (Fetch Plans)
- Dynamic attributes

### Load by ID
```java
Customer customer = dataManager.load(Customer.class)
        .id(customerId)
        .fetchPlan("customer-full")
        .one();
```

### Load List with Query
```java
List<Order> orders = dataManager.load(Order.class)
        .query("select o from Order o where o.customer = :customer")
        .parameter("customer", customer)
        .list();
```

### Save / Remove
```java
// Save one or more entities
dataManager.save(order, customer);

// Save with SaveContext (complex operations)
SaveContext saveContext = new SaveContext();
saveContext.saving(order);
saveContext.saving(customer);
saveContext.removing(deletedOrderLines);
dataManager.save(saveContext);

// Remove
dataManager.remove(entity);
```

## Advanced: Transactional EntityManager
Use `EntityManager` strictly when you need:
- Bulk updates/deletes (JPQL)
- Native SQL
- Stored procedures
- Bypassing some Jmix overhead (careful!)

**Rules:**
1. Must use `@PersistenceContext` to inject.
2. Must be inside `@Transactional`.
3. Does **not** apply Jmix row-level security automatically.

```java
package com.company.project.service;

import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class BulkOperationService {

    @PersistenceContext
    private EntityManager entityManager;

    @Transactional
    public void archiveOldOrders() {
        // Example: Bulk update using JPQL
        int updatedCount = entityManager.createQuery(
                "update Order o set o.status = :newStatus where o.date < :cutoff")
                .setParameter("newStatus", OrderStatus.ARCHIVED)
                .setParameter("cutoff", LocalDate.now().minusYears(1))
                .executeUpdate();
                
        // Native SQL example
        entityManager.createNativeQuery("UPDATE ORDERS SET ARCHIVED = TRUE WHERE DATE < ?1")
                .setParameter(1, LocalDate.now().minusYears(1))
                .executeUpdate();
    }
}
```

## Transactions
Jmix 2.x uses standard Spring `@Transactional`.

```java
@Transactional
public void complexOperation() {
    // Operations here are atomic
    // If exception is thrown, all changes roll back
    Order order = dataManager.save(newOrder);
    processPayment(order); // if this fails, order save is rolled back
}

@Transactional(readOnly = true)
public List<Order> getReport() {
    // Optimization for read-only operations
    return dataManager.load(Order.class).all().list();
}
```

## Programmatic Transactions (TransactionTemplate)
Use this when AOP (`@Transactional`) is not applicable or when you need fine-grained control over transaction boundaries within a method.

```java
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.support.TransactionTemplate;

@Service
public class ManualTxService {

    private final TransactionTemplate transactionTemplate;
    private final DataManager dataManager;

    public ManualTxService(PlatformTransactionManager transactionManager,
                           DataManager dataManager) {
        this.transactionTemplate = new TransactionTemplate(transactionManager);
        this.dataManager = dataManager;
    }

    public void executeInTransaction() {
        String result = transactionTemplate.execute(status -> {
            try {
                // Code running in transaction
                Order order = dataManager.create(Order.class);
                dataManager.save(order);
                
                // You can manually rollback if needed
                if (someConditionFails()) {
                    status.setRollbackOnly();
                    return "Rolled back";
                }
                
                return "Committed";
            } catch (Exception e) {
                // Exceptions automatically trigger rollback usually,
                // but setting it explicitly is safe
                status.setRollbackOnly();
                throw e;
            }
        });
    }
}
```

## Naming Conventions & Stereotypes

### Bean Naming (Prefixing)
To avoid bean name collisions in large projects or when creating add-ons, **always prefix your bean names**.
- If your project/module has a prefix (e.g., `bpm`), use it: `@Service("bpm_MyService")`.
- If no specific prefix, use the project name or generic `app_`: `@Service("app_MyService")`.

### Stereotypes
- **`@Service`**: Use for **Business Logic**. These are your transaction boundaries and domain operations.
- **`@Component`**: Use for **Infrastructure**, **Utils**, **Listeners**, or **Configs**.
- **`@Bean` methods**: Follow the naming convention `app_BeanName`.

```java
@Service("app_OrderService") // Explicit name with prefix
public class OrderService { ... }

@Component("app_OrderEventListener")
public class OrderEventListener { ... }

@Configuration
public class MyConfig {
    @Bean("app_CustomMapper") // Explicit name
    public ObjectMapper customMapper() { ... }
}
```

## Jmix Data Repositories
For simple CRUD, you can use Jmix Data Repositories (Spring Data style) instead of `DataManager`.

### Rules
1. Extend `JmixDataRepository<Entity, ID>`.
2. To use Fetch Plans, pass `FetchPlan` (or `String` plan name) as the **last parameter** of the method.

```java
import io.jmix.core.repository.JmixDataRepository;
import io.jmix.core.FetchPlan;
import java.util.UUID;
import java.util.List;

public interface UserRepository extends JmixDataRepository<User, UUID> {
    
    // Standard Spring Data method
    List<User> findByActiveTrue();
    
    // Jmix: Pass FetchPlan as the last argument
    List<User> findByLastName(String lastName, FetchPlan fetchPlan);
    
    // Works with @Query too
    @Query("select u from User u where u.email like :email")
    User findUserByEmail(@Param("email") String email, FetchPlan fetchPlan);
}
```

## Checklist
- [ ] `@Service` used for business logic, `@Component` for others
- [ ] **Bean Name Prefix** applied (`app_` or `project_`)
- [ ] Dependencies injected (Constructor preferred)
- [ ] `DataManager` or `JmixDataRepository` used
- [ ] `FetchPlan` to load entity's properties graph
- [ ] `EntityManager` used ONLY for bulk/native operations
- [ ] Business logic resides here, not in Views

## Forbidden
- Using `EntityManager` without `@Transactional`
- Modifying UI components (`TextField`, etc.) inside a Service
- Manual transaction handling (raw `tm.begin()`). Use `@Transactional` by default. Use `TransactionTemplate` ONLY when AOP is not applicable (e.g., self-invocation or aspect conflicts).