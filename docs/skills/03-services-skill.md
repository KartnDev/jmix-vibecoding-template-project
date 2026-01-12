# Skill: Services

## Описание
Бизнес-логика в сервисах с DataManager.

## Паттерн
```java
@Service
public class MyService {
    private final DataManager dataManager;
    public MyService(DataManager dataManager) { this.dataManager = dataManager; }
}
```

## CRUD через DataManager
- load by id/query, save, remove
- fetch plans для выборок

## Транзакции
- `@Transactional` на методах/классе по необходимости

## Чеклист
- Конструкторная инъекция
- Нет EntityManager
- Нет field `@Autowired`
- Нет hardcoded creds

## Запрещено
- EntityManager
- Field `@Autowired`
