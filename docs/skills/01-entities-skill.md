# Skill: Entities (Jmix)

## Описание
Создание сущностей с Jmix-аннотациями.

## Шаблон
```java
@JmixEntity
@Entity
@Table(name = "ENTITY_NAME")
public class EntityName {
    @Id @JmixGeneratedValue @Column(name = "ID") UUID id;
    @Version @Column(name = "VERSION", nullable = false) Integer version;
    @InstanceName @Column(name = "NAME") String name;
}
```

## Связи
- ManyToOne LAZY + @JoinColumn
- OneToMany mappedBy
- Composition + cascade delete при необходимости

## Liquibase
- Changelog в `.../liquibase/changelog/`
- UUID PK, VERSION
- Добавить include в `changelog.xml`
- Добавить messages в `messages_en.properties`

## Чеклист
- UUID + Version + InstanceName
- Liquibase создан и включён
- messages обновлены

## Запрещено
- Lombok на entities
- Field `@Autowired`
- EntityManager
