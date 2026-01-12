# Skill: Liquibase

## Описание
Миграции БД для Jmix.

## Паттерн changelog (create table)
- UUID PK (`type="uuid"`, PK constraint)
- VERSION (`int`, not null)
- Бизнес-поля с явными типами
- Файл в `.../liquibase/changelog/`
- Include в `changelog.xml`

## Индексы/связи
- FK: `addForeignKeyConstraint`
- Индексы: `createIndex` (уникальные при необходимости)
- Join таблицы для many-to-many

## Чеклист
- Файл создан
- Включён в `changelog.xml`
- PK UUID + VERSION
- Соответствует entity полям

## Запрещено
- bigint/serial PK
- Пропуск VERSION
