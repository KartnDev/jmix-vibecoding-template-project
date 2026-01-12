# Skill: Entities

## Description
Creating JPA entities with Jmix annotations for proper integration with the framework.

## Entity Template
```java
package com.company.project.entity;

import io.jmix.core.entity.annotation.JmixGeneratedValue;
import io.jmix.core.metamodel.annotation.InstanceName;
import io.jmix.core.metamodel.annotation.JmixEntity;
import jakarta.persistence.*;
import jakarta.validation.constraints.NotNull;
import java.util.UUID;

@JmixEntity
@Table(name = "CUSTOMER")
@Entity
public class Customer {

    @JmixGeneratedValue
    @Column(name = "ID", nullable = false)
    @Id
    private UUID id;

    @Column(name = "VERSION", nullable = false)
    @Version
    private Integer version;

    @InstanceName
    @Column(name = "NAME", nullable = false)
    @NotNull
    private String name;

    @Column(name = "EMAIL")
    private String email;

    // Getters and setters (NO Lombok!)
    public UUID getId() { return id; }
    public void setId(UUID id) { this.id = id; }
    // ... other getters/setters
}
```

## Required Annotations
| Annotation | Purpose |
|------------|---------|
| `@JmixEntity` | Registers entity in Jmix metadata |
| `@Entity` + `@Table` | Standard JPA |
| `@JmixGeneratedValue` | UUID auto-generation |
| `@Version` | Optimistic locking |
| `@InstanceName` | Display name in UI (dropdowns, references) |

## Relationships

### ManyToOne (Lazy)
```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "CUSTOMER_ID")
private Customer customer;
```

### OneToMany
```java
@OneToMany(mappedBy = "order")
private List<OrderLine> lines;
```

### Composition (Parent-Child with Cascade Delete)
```java
@Composition
@OnDelete(DeletePolicy.CASCADE)
@OneToMany(mappedBy = "order")
private List<OrderLine> lines;
```

## Validation Annotations
```java
@NotNull
@Email
@Size(min = 1, max = 255)
@Pattern(regexp = "...")
@Positive
@PastOrPresent
```

## Liquibase Changelog
Create in `src/main/resources/.../liquibase/changelog/`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog ...>
    <changeSet id="1" author="dev">
        <createTable tableName="CUSTOMER">
            <column name="ID" type="UUID">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="VERSION" type="INT">
                <constraints nullable="false"/>
            </column>
            <column name="NAME" type="VARCHAR(255)">
                <constraints nullable="false"/>
            </column>
            <column name="EMAIL" type="VARCHAR(255)"/>
        </createTable>
    </changeSet>
</databaseChangeLog>
```

Add include in `changelog.xml`:
```xml
<include file="020-customer.xml" relativeToChangelogFile="true"/>
```

But user also can generate changelog by using Jmix Studio on his own.

## Messages
Add to `messages_en.properties`:
```properties
com.company.project.entity/Customer=Customer
com.company.project.entity/Customer.name=Name
com.company.project.entity/Customer.email=Email
```

## Checklist
- [ ] `@JmixEntity` + `@Entity` + `@Table`
- [ ] UUID ID with `@JmixGeneratedValue`
- [ ] `@Version` field
- [ ] `@InstanceName` on display field
- [ ] Liquibase changelog created
- [ ] Changelog included in `changelog.xml`
- [ ] Messages added

## Forbidden
- Lombok annotations (`@Data`, `@Getter`, etc.)
- Field `@Autowired`
- `@GeneratedValue` (use `@JmixGeneratedValue`)
- EntityManager usage (w/o necessary transactions)
