# Skill: Security (Roles & Policies)

## Description
Configuring access control with Resource Roles and Row-Level Roles.

## Resource Role Template
```java
package com.company.project.security;

import com.company.project.entity.Customer;
import com.company.project.entity.Order;
import io.jmix.security.model.EntityAttributePolicyAction;
import io.jmix.security.model.EntityPolicyAction;
import io.jmix.security.role.annotation.*;

@ResourceRole(name = "Sales Manager", code = SalesManagerRole.CODE)
public interface SalesManagerRole {

    String CODE = "sales-manager";

    // Full access to Customer entity
    @EntityPolicy(entityClass = Customer.class, actions = EntityPolicyAction.ALL)
    @EntityAttributePolicy(entityClass = Customer.class, attributes = "*", action = EntityAttributePolicyAction.MODIFY)
    void customer();

    // Read-only access to Order
    @EntityPolicy(entityClass = Order.class, actions = {EntityPolicyAction.READ})
    @EntityAttributePolicy(entityClass = Order.class, attributes = "*", action = EntityAttributePolicyAction.VIEW)
    void order();

    // View access
    @ViewPolicy(viewIds = {"Customer.list", "Customer.detail", "Order.list"})
    void views();

    // Menu access
    @MenuPolicy(menuIds = {"Customer.list", "Order.list"})
    void menu();

    // Specific operations
    @SpecificPolicy(resources = "order.approve")
    void approveOrder();
}
```

## Entity Policies

### All Operations
```java
@EntityPolicy(entityClass = Customer.class, actions = EntityPolicyAction.ALL)
```

### Specific Operations
```java
@EntityPolicy(entityClass = Order.class, actions = {
    EntityPolicyAction.CREATE,
    EntityPolicyAction.READ,
    EntityPolicyAction.UPDATE
})
// No DELETE
```

### Read-Only
```java
@EntityPolicy(entityClass = Product.class, actions = EntityPolicyAction.READ)
```

## Attribute Policies

### Full Access
```java
@EntityAttributePolicy(entityClass = Customer.class, attributes = "*", action = EntityAttributePolicyAction.MODIFY)
```

### View Only
```java
@EntityAttributePolicy(entityClass = Customer.class, attributes = "*", action = EntityAttributePolicyAction.VIEW)
```

### Specific Attributes
```java
@EntityAttributePolicy(entityClass = Customer.class, attributes = {"name", "email"}, action = EntityAttributePolicyAction.MODIFY)
@EntityAttributePolicy(entityClass = Customer.class, attributes = {"internalNotes"}, action = EntityAttributePolicyAction.VIEW)
```

### Hide Attributes
```java
@EntityAttributePolicy(entityClass = Customer.class, attributes = {"salary"}, action = EntityAttributePolicyAction.HIDE)
```

## Row-Level Role (Data Filtering)
```java
package com.company.project.security;

import com.company.project.entity.Order;
import io.jmix.security.role.annotation.JpqlRowLevelPolicy;
import io.jmix.security.role.annotation.RowLevelRole;

@RowLevelRole(name = "See Own Orders", code = SeeOwnOrdersRole.CODE)
public interface SeeOwnOrdersRole {

    String CODE = "see-own-orders";

    @JpqlRowLevelPolicy(
        entityClass = Order.class,
        where = "{E}.createdBy = :current_user_username"
    )
    void order();
}
```

## Predicate-Based Row-Level Policy
```java
@RowLevelRole(name = "Regional Manager", code = "regional-manager")
public interface RegionalManagerRole {

    @PredicateRowLevelPolicy(entityClass = Customer.class)
    default RowLevelBiPredicate<Customer, ApplicationContext> customerPolicy() {
        return (customer, ctx) -> {
            UserDetails user = ctx.getBean(CurrentAuthentication.class).getUser();
            // Custom logic
            return customer.getRegion().equals(getUserRegion(user));
        };
    }
}
```

## Built-in Roles
- `system-full-access` — Full access to everything
- `ui-minimal` — Minimal UI access (login, main screen)

## Checking Permissions in Code
```java
@Autowired
private AccessManager accessManager;

public void checkAccess(Order order) {
    if (!accessManager.isEntityOpPermitted(Order.class, EntityOp.UPDATE)) {
        throw new AccessDeniedException("order", "Cannot update order");
    }
}
```

## Specific Policies
```java
// In role
@SpecificPolicy(resources = "order.approve")
void approveOrder();

// Check in code
@Autowired
private AccessManager accessManager;

if (accessManager.isSpecificPermitted("order.approve")) {
    // Allow action
}
```

## Checklist
- [ ] Role code defined as constant
- [ ] Entity policies (CRUD level)
- [ ] Attribute policies (modify/view/hide)
- [ ] View policies for screens
- [ ] Menu policies for menu items
- [ ] Row-level policies if needed

## Forbidden
- Hardcoded passwords/secrets
- Bypassing security in code
- Using deprecated `@Role` annotation
