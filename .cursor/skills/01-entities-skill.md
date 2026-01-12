# Skill: Entities

## Description
Creating JPA entities with Jmix annotations for proper integration with the framework.

## Imports
```java
import io.jmix.core.entity.annotation.JmixGeneratedValue;
import io.jmix.core.metamodel.annotation.DependsOnProperties;
import io.jmix.core.metamodel.annotation.InstanceName;
import io.jmix.core.metamodel.annotation.JmixEntity;
import io.jmix.core.metamodel.annotation.JmixProperty;
import jakarta.persistence.*;
import jakarta.validation.constraints.NotNull;
import java.util.UUID;
```

## Why Both @JmixEntity and @Entity?
| Annotation | Purpose |
|------------|---------|
| `@Entity` | JPA/Hibernate persistence |
| `@JmixEntity` | Jmix metadata — enables UI binding, security, fetch plans, DataManager |

**Both required!** Without `@JmixEntity` — entity won't appear in UI, security won't apply.

## Entity Template
```java
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
| `@JmixGeneratedValue` | UUID auto-generation (application-level, not DB) |
| `@Version` | Optimistic locking |
| `@InstanceName` | Display name in UI (dropdowns, references) |

## @InstanceName on Method
For computed display names:
```java
@InstanceName
@DependsOnProperties({"firstName", "lastName", "username"})
public String getDisplayName() {
    return String.format("%s %s [%s]", 
        firstName != null ? firstName : "",
        lastName != null ? lastName : "", 
        username).trim();
}
```
**`@DependsOnProperties` is required** — tells Jmix which fields to load for fetch plans.

## Audit Fields (Optional)
```java
import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;

@CreatedBy
@Column(name = "CREATED_BY")
private String createdBy;

@CreatedDate
@Column(name = "CREATED_DATE")
private OffsetDateTime createdDate;

@LastModifiedBy
@Column(name = "LAST_MODIFIED_BY")
private String lastModifiedBy;

@LastModifiedDate
@Column(name = "LAST_MODIFIED_DATE")
private OffsetDateTime lastModifiedDate;
```
Jmix populates these automatically via DataManager.

## Soft Delete (Optional)
```java
import org.springframework.data.annotation.DeletedBy;
import org.springframework.data.annotation.DeletedDate;

@DeletedBy
@Column(name = "DELETED_BY")
private String deletedBy;

@DeletedDate
@Column(name = "DELETED_DATE")
private OffsetDateTime deletedDate;
```
- `DataManager.remove()` sets these fields instead of DELETE
- Soft-deleted entities filtered automatically from queries
- Use `PersistenceHints.SOFT_DELETION = false` to load deleted

## Relationships

### ManyToOne (Always LAZY!)
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
import io.jmix.core.DeletePolicy;
import io.jmix.core.entity.annotation.OnDelete;
import io.jmix.core.metamodel.annotation.Composition;

@Composition                          // Child is part of parent
@OnDelete(DeletePolicy.CASCADE)       // Delete children when parent deleted
@OneToMany(mappedBy = "order")
private List<OrderLine> lines;
```

### Delete Policies
| Policy | Behavior |
|--------|----------|
| `CASCADE` | Delete related entities |
| `DENY` | Prevent deletion if related exist |
| `SET_NULL` | Set FK to null |
| `UNLINK` | Remove from collection without deleting |

## Embedded Objects (Value Objects)
```java
@JmixEntity
@Embeddable
public class Address {
    @Column(name = "CITY")
    private String city;
    
    @Column(name = "STREET")
    private String street;
    // getters/setters
}

// In entity:
@Embedded
@AttributeOverrides({
    @AttributeOverride(name = "city", column = @Column(name = "HOME_CITY")),
    @AttributeOverride(name = "street", column = @Column(name = "HOME_STREET"))
})
private Address homeAddress;
```

## Transient/Calculated Fields
```java
@JmixProperty
@Transient
public BigDecimal getTotal() {
    return price.multiply(BigDecimal.valueOf(quantity));
}
```
`@JmixProperty` exposes transient getter to Jmix metadata (UI, filters).

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

## equals() and hashCode()
**Recommendation:** Don't override unless necessary.

If needed, use ID-based with stable hashCode:
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Customer that = (Customer) o;
    return id != null && id.equals(that.id);
}

@Override
public int hashCode() {
    return getClass().hashCode();  // Stable for Hibernate collections
}
```

## Entity Events
Prefer Spring `@EventListener` over JPA `@PrePersist`/`@PostLoad`:
```java
@Component
public class OrderEntityListener {
    @EventListener
    public void onBeforeSave(EntitySavingEvent<Order> event) {
        Order order = event.getEntity();
        // business logic
    }
}
```

## Forbidden
- Lombok annotations (`@Data`, `@Getter`, etc.)
- Field `@Autowired`
- `@GeneratedValue` (use `@JmixGeneratedValue`)
- `@PostConstruct` in entities (not Spring beans)
- `FetchType.EAGER` on relationships
- EntityManager for regular CRUD (use DataManager)
- Business logic in entity methods
