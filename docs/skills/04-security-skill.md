# Skill: Security

## Описание
Роли и политики в Jmix.

## Паттерн роли
```java
@ResourceRole(name = "Role", code = Role.CODE)
public class Role {
    public static final String CODE = "role";

    @EntityPolicy(entityClass = Entity.class, actions = {EntityPolicyAction.ALL})
    @EntityAttributePolicy(entityClass = Entity.class, attributes = "*", action = EntityAttributePolicyAction.MODIFY)
    void entity() {}

    @ViewPolicy(viewIds = {"Entity.list", "Entity.detail"})
    @MenuPolicy(menuIds = {"Entity.list"})
    void screens() {}
}
```

## Row-level
- `@RowLevelRole` + `@JpqlRowLevelPolicy` или `@PredicatePolicy`

## Чеклист
- CODE константа
- Entity policies заданы
- View/Menu policies заданы

## Запрещено
- Hardcoded passwords
- Bypass security
