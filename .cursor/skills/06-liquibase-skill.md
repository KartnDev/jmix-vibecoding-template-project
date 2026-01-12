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
            <column name="CREATED_BY" type="VARCHAR(255)"/>
            <column name="CREATED_DATE" type="TIMESTAMP"/>
            <column name="LAST_MODIFIED_BY" type="VARCHAR(255)"/>
            <column name="LAST_MODIFIED_DATE" type="TIMESTAMP"/>
        </createTable>
    </changeSet>

</databaseChangeLog>
```

## Column Types

| Java Type | Liquibase Type |
|-----------|----------------|
| UUID | `UUID` |
| String | `VARCHAR(n)` |
| Integer | `INT` |
| Long | `BIGINT` |
| BigDecimal | `DECIMAL(p,s)` |
| Boolean | `BOOLEAN` |
| LocalDate | `DATE` |
| LocalDateTime | `TIMESTAMP` |
| LocalTime | `TIME` |
| byte[] | `BLOB` |
| Enum | `VARCHAR(50)` or `INT` |

## Required Columns
Every Jmix entity table must have:
```xml
<column name="ID" type="UUID">
    <constraints primaryKey="true" nullable="false"/>
</column>
<column name="VERSION" type="INT">
    <constraints nullable="false"/>
</column>
```

## Foreign Keys
```xml
<changeSet id="2" author="dev">
    <createTable tableName="ORDER_">
        <column name="ID" type="UUID">
            <constraints primaryKey="true" nullable="false"/>
        </column>
        <column name="VERSION" type="INT">
            <constraints nullable="false"/>
        </column>
        <column name="CUSTOMER_ID" type="UUID">
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

## Forbidden
- Auto-increment IDs (use UUID)
- Missing VERSION column
- Same changeset ID in different files
- Modifying already-applied changesets
