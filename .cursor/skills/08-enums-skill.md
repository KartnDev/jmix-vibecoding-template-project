# Skill: Enums

## Description
Creating enumerations for entity fields with proper localization.

## Required Imports
```java
import io.jmix.core.metamodel.datatype.EnumClass;
import org.springframework.lang.Nullable; // For fromId() return
```

## Enum Template
```java
package com.company.project.entity;

import io.jmix.core.metamodel.datatype.EnumClass;
import org.springframework.lang.Nullable;

public enum OrderStatus implements EnumClass<String> {

    NEW("NEW"),
    CONFIRMED("CONFIRMED"),
    PROCESSING("PROCESSING"),
    SHIPPED("SHIPPED"),
    DELIVERED("DELIVERED"),
    CANCELLED("CANCELLED");

    private final String id;

    OrderStatus(String id) {
        this.id = id;
    }

    @Override
    public String getId() {
        return id;
    }

    @Nullable
    public static OrderStatus fromId(String id) {
        for (OrderStatus status : OrderStatus.values()) {
            if (status.getId().equals(id)) {
                return status;
            }
        }
        return null;
    }
}
```

## EnumClass Interface
Jmix enums should implement `EnumClass<T>` where T is the database storage type:
- `EnumClass<String>` — stored as VARCHAR
- `EnumClass<Integer>` — stored as INT

## Alternative: EnumUtils.fromId()
Instead of writing custom `fromId()` method in every enum, use Jmix utility:

```java
import io.jmix.core.metamodel.datatype.EnumUtils;

// EnumUtils.fromId(TargetEnum.class, storedValue)

// In entity getter
public OrderStatus getStatus() {
    return EnumUtils.fromId(OrderStatus.class, this.status);
}
```

This is shorter and handles null safely.

## Integer-Based Enum
```java
public enum Priority implements EnumClass<Integer> {

    LOW(10),
    MEDIUM(20),
    HIGH(30),
    CRITICAL(40);

    private final Integer id;

    Priority(Integer id) {
        this.id = id;
    }

    @Override
    public Integer getId() {
        return id;
    }

    @Nullable
    public static Priority fromId(Integer id) {
        for (Priority priority : Priority.values()) {
            if (priority.getId().equals(id)) {
                return priority;
            }
        }
        return null;
    }
}
```

## Using Enum in Entity
```java
@JmixEntity
@Entity
@Table(name = "ORDER_")
public class Order {

    // ... id, version fields

    @Column(name = "STATUS")
    private String status;

    public OrderStatus getStatus() {
        return status == null ? null : OrderStatus.fromId(status);
    }

    public void setStatus(OrderStatus status) {
        this.status = status == null ? null : status.getId();
    }
}
```

## Liquibase for Enum Column
```xml
<!-- String-based enum -->
<column name="STATUS" type="VARCHAR(50)"/>

<!-- Integer-based enum -->
<column name="PRIORITY" type="INT"/>
```

## Messages (Localization)
Add to `messages_en.properties`:
```properties
com.company.project.entity/OrderStatus=Order Status
com.company.project.entity/OrderStatus.NEW=New
com.company.project.entity/OrderStatus.CONFIRMED=Confirmed
com.company.project.entity/OrderStatus.PROCESSING=Processing
com.company.project.entity/OrderStatus.SHIPPED=Shipped
com.company.project.entity/OrderStatus.DELIVERED=Delivered
com.company.project.entity/OrderStatus.CANCELLED=Cancelled
```

## Enum in UI (XML)
```xml
<!-- ComboBox -->
<comboBox id="statusField" property="status"/>

<!-- DataGrid column (automatic formatting) -->
<column property="status"/>
```

## Filtering by Enum
```java
List<Order> orders = dataManager.load(Order.class)
        .query("select o from Order o where o.status = :status")
        .parameter("status", OrderStatus.NEW.getId())
        .list();
```

## Enum with Additional Fields
```java
public enum OrderStatus implements EnumClass<String> {

    NEW("NEW", "badge-info", true),
    CONFIRMED("CONFIRMED", "badge-primary", true),
    PROCESSING("PROCESSING", "badge-warning", true),
    SHIPPED("SHIPPED", "badge-success", true),
    DELIVERED("DELIVERED", "badge-success", false),
    CANCELLED("CANCELLED", "badge-error", false);

    private final String id;
    private final String badgeClass;
    private final boolean editable;

    OrderStatus(String id, String badgeClass, boolean editable) {
        this.id = id;
        this.badgeClass = badgeClass;
        this.editable = editable;
    }

    @Override
    public String getId() { return id; }
    
    public String getBadgeClass() { return badgeClass; }
    
    public boolean isEditable() { return editable; }

    // fromId method...
}
```

## Checklist
- [ ] Implements `EnumClass<T>`
- [ ] Has `getId()` method
- [ ] Has static `fromId()` method
- [ ] Messages for all values in `messages_en.properties`
- [ ] Proper column type in Liquibase

## Forbidden
- Plain Java enums without `EnumClass` (breaks UI localization)
- `@Convert` / `AttributeConverter` (use EnumClass instead)
- Storing enum `.name()` directly (refactoring breaks existing data)
- Missing `@Nullable` on `fromId()` return
- Entity field typed as enum directly (must store ID as String/Integer)
