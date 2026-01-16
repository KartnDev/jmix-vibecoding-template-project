# AI Agents Benchmark - Session State

## Prompt для продолжения

```
Мы тестируем AI агентов на задаче "написать петклинику" в Jmix проекте.

Проект: /Users/dmitrycherkasov/IdeaProjects/migrationcuba/shamrock-jmix-migrated/jmix-petclinic-junie

Бенчмарк файл: /Users/dmitrycherkasov/Desktop/AI_AGENTS_PETCLINIC_BENCHMARK.md

## Что уже сделано:

### Протестированы:
1. **Junie (GPT-5.1 Codex Max)**: 34→45/100 (6 промптов)
   - Не компилировался, много ошибок, не читал AI контекст

2. **Junie (Opus 4.5)**: 52/100 (1 промпт, one-shot)
   - Прочитал CLAUDE.md, знает Jmix, но не использовал MCP

### Ожидают тестирования:
- [ ] Junie (Sonnet 4.5)
- [ ] Junie (Gemini 3 Flash)
- [ ] Junie (Gemini 3 Pro)
- [ ] Cursor (Default)
- [ ] Cursor (ChatGPT)
- [ ] Cursor (Sonnet 4.5)
- [ ] Cursor (Gemini 3 Pro/Flash)
- [ ] Continue (Anthropic API)

## Ключевые находки:

### Junie Platform Bugs:
- НЕ читает `AGENTS.md` (хотя это "будущий стандарт")
- НЕ читает dotfiles КРОМЕ `.junie/`
- НЕ использует MCP инструменты проактивно
- НЕ верифицирует свой код

### Структура AI файлов в проекте:
- `.junie/guidelines.md` - для Junie (AUTO-READ)
- `CLAUDE.md` - для Claude Code (AUTO-READ)
- `AGENTS.md` - НЕ работает в Junie!
- `ai-docs/rules/` и `ai-docs/skills/` - универсальные правила
- `.cursor/` - только для Cursor

## Команды для тестирования:

# Перед каждым тестом:
git reset --hard HEAD

# Промпт для агента:
"Можешь написать петклинику в данном проекте"

## Метрики оценки (каждая 0-10):
1. Compilation - компилируется ли
2. Entities - все 6 сущностей (Owner, Pet, PetType, Visit, Vet, Specialty)
3. Views - list + detail для каждой сущности
4. Liquibase - правильная схема БД
5. i18n - сообщения локализации
6. Menu - интеграция в меню
7. Rules - следование AI правилам
8. Quality - качество кода
9. Services - бизнес-логика
10. Tests - тесты

TOTAL = сумма всех метрик (/100)
```

---

## Текущие результаты

| Agent | Compile | Entities | Views | Liquibase | i18n | Menu | Rules | Quality | Services | Tests | TOTAL | Prompts |
|-------|---------|----------|-------|-----------|------|------|-------|---------|----------|-------|-------|---------|
| Junie (GPT-5.1 Codex) | 0→5 | 7 | 3→6 | 9 | 7 | 1→5 | 0 | 5→6 | 0 | 0 | 34→45 | 6 |
| Junie (Opus 4.5) | 7 | 8 | 7 | 9 | 7 | 6 | 1 | 7 | 0 | 0 | 52 | 1 |

---

## Выводы на данный момент

**Junie Platform Ceiling: ~50-55 points**

Независимо от модели, Junie ограничен:
- Не читает AGENTS.md
- Не использует MCP
- Не самопроверяется

Opus vs GPT: +18 очков разницы (сила модели), но платформа ограничивает обоих.
