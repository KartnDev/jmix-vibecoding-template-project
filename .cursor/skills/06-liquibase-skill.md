# Skill: Liquibase (Database Migrations)

## Description
Creating database changelogs for Jmix entities.

## Changelog Template
```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-latest.xsd">

    <changeSet id="020-customer-1" author="yourproject">
        <createTable tableName="CUSTOMER">
            <column name="ID" type="${uuid.type}">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="VERSION" type="int">
                <constraints nullable="false"/>
            </column>
            <column name="NAME" type="varchar(255)">
                <constraints nullable="false"/>
            </column>
            <column name="EMAIL" type="varchar(255)"/>
            <column name="CREATED_BY" type="varchar(255)"/>
            <column name="CREATED_DATE" type="timestamp"/>
            <column name="LAST_MODIFIED_BY" type="varchar(255)"/>
            <column name="LAST_MODIFIED_DATE" type="timestamp"/>
        </createTable>
        <rollback>
            <dropTable tableName="CUSTOMER"/>
        </rollback>
    </changeSet>

</databaseChangeLog>
```

## Changeset ID Convention
- **Format:** `NNN-entity-N` (e.g., `020-customer-1`) or `YYYYMMDD-HHMM-topic`
- **Must be globally unique** across all changelog files
- **Author:** Use project name or your username (NOT generic `dev`)
- **Never modify applied changesets** — create new ones instead

## Column Types

| Java Type | Liquibase Type |
|-----------|----------------|
| UUID | `${uuid.type}` (cross-database!) |
| String | `varchar(n)` |
| Integer | `int` |
| Long | `bigint` |
| BigDecimal | `decimal(p,s)` |
| Boolean | `boolean` |
| LocalDate | `date` |
| LocalDateTime | `timestamp` |
| LocalTime | `time` |
| byte[] | `blob` |
| Enum | `varchar(50)` or `int` |

## Required Columns
Every Jmix entity table must have:
```xml
<column name="ID" type="${uuid.type}">
    <constraints primaryKey="true" nullable="false"/>
</column>
<column name="VERSION" type="int">
    <constraints nullable="false"/>
</column>
```

## Audit Columns (Optional)
If entity uses `@CreatedBy`, `@LastModifiedBy` etc.:
```xml
<column name="CREATED_BY" type="varchar(255)"/>
<column name="CREATED_DATE" type="timestamp"/>
<column name="LAST_MODIFIED_BY" type="varchar(255)"/>
<column name="LAST_MODIFIED_DATE" type="timestamp"/>
```

## Foreign Keys
```xml
<changeSet id="020-order-1" author="project">
    <createTable tableName="ORDER_">
        <column name="ID" type="${uuid.type}">
            <constraints primaryKey="true" nullable="false"/>
        </column>
        <column name="VERSION" type="int">
            <constraints nullable="false"/>
        </column>
        <column name="CUSTOMER_ID" type="${uuid.type}">
            <constraints nullable="false"/>
        </column>
    </createTable>

    <addForeignKeyConstraint
            baseTableName="ORDER_"
            baseColumnNames="CUSTOMER_ID"
            constraintName="FK_ORDER_CUSTOMER"
            referencedTableName="CUSTOMER"
            referencedColumnNames="ID"/>
</changeSet>
```

## Indexes
```xml
<createIndex tableName="CUSTOMER" indexName="IDX_CUSTOMER_EMAIL">
    <column name="EMAIL"/>
</createIndex>

<!-- Unique index -->
<createIndex tableName="CUSTOMER" indexName="IDX_CUSTOMER_EMAIL_UNQ" unique="true">
    <column name="EMAIL"/>
</createIndex>
```

## Add Column
```xml
<changeSet id="3" author="dev">
    <addColumn tableName="CUSTOMER">
        <column name="PHONE" type="VARCHAR(50)"/>
    </addColumn>
</changeSet>
```

## Modify Column
```xml
<changeSet id="4" author="dev">
    <modifyDataType tableName="CUSTOMER" columnName="NAME" newDataType="VARCHAR(500)"/>
</changeSet>
```

## Drop Column
```xml
<changeSet id="5" author="dev">
    <dropColumn tableName="CUSTOMER" columnName="OLD_FIELD"/>
</changeSet>
```

## Include in Master Changelog
`src/main/resources/.../liquibase/changelog.xml`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog ...>
    <include file="com/company/project/liquibase/changelog/010-init-user.xml"/>
    <include file="com/company/project/liquibase/changelog/020-customer.xml"/>
    <include file="com/company/project/liquibase/changelog/030-order.xml"/>
</databaseChangeLog>
```

## Naming Convention
- Files: `NNN-entity-name.xml` (e.g., `020-customer.xml`)
- Tables: `UPPER_SNAKE_CASE` (e.g., `ORDER_LINE`)
- Columns: `UPPER_SNAKE_CASE` (e.g., `CUSTOMER_ID`)
- Constraints: `FK_TABLE_REFERENCE`, `IDX_TABLE_COLUMN`

## Reserved Words
Avoid SQL reserved words for table names. If unavoidable, suffix with underscore:
- `ORDER` → `ORDER_`
- `USER` → `USER_` (already exists in Jmix)
- `GROUP` → `GROUP_`

## Checklist
- [ ] UUID primary key
- [ ] VERSION column (INT, not null)
- [ ] Proper column types
- [ ] Foreign keys with constraints
- [ ] Indexes for search columns
- [ ] Included in `changelog.xml`
- [ ] Changeset IDs are unique

## Context (Environment-Specific)
Use `context` to control which changesets run in which environments:
```xml
<!-- Only runs in dev -->
<changeSet id="030-testdata-1" author="yourproject" context="dev">
    <insert tableName="CUSTOMER">...</insert>
</changeSet>

<!-- Exclude from production -->
<changeSet id="030-testdata-2" author="yourproject" context="!prod">
    ...
</changeSet>
```

Configure in `application.properties`:
```properties
spring.liquibase.contexts=dev
```

## Rollback Strategy
Always provide explicit rollback for DDL:
```xml
<changeSet id="020-customer-1" author="yourproject">
    <createTable tableName="CUSTOMER">...</createTable>
    <rollback>
        <dropTable tableName="CUSTOMER"/>
    </rollback>
</changeSet>
```

**For production fixes:** Never modify applied changesets. Create compensating changesets instead.

## Forbidden
- `type="UUID"` (use `${uuid.type}` for cross-DB)
- `id="1"` without file prefix (collision risk)
- `author="dev"` (use meaningful name)
- Auto-increment IDs (use UUID)
- Missing VERSION column
- Modifying already-applied changesets
