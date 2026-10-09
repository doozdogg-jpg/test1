# Проверка CI

## Событие, ветка и SHA
- Workflow: .github/workflows/ci.yml (push в main, pull_request, workflow_dispatch), матрица Python 3.11 и 3.12.
- Исходный запуск на main: SHA 939265f — URL: <ВСТАВИТЬ после push>
- Исходный набор: 6 методов, OK.

## Красный запуск
- Ветка ci/boundary, SHA c604841, событие pull_request. URL: https://github.com/doozdogg-jpg/test1/actions/runs/37996911031
- Job: tests (3.11) и tests (3.12); упавший step: Run tests.
- Упали test_at_limit, test_high_priority, test_low_priority. Ожидалось False, получено True (AssertionError: True is not false).
- Причина: в sla.py оператор > заменён на >=, на границе лимита функция стала возвращать True, а по REQUIREMENTS.md просрочка только строго после лимита.

## Исправление
- SHA ec3107c: оператору возвращено >; diff относительно красного коммита — одна строка условия.

## Зелёный запуск на Python 3.11 и 3.12
- SHA faa4cca (ветка lab/boundary, PR #1): https://github.com/doozdogg-jpg/test1/actions/runs/37996502987
- SHA 7ad19bc (после merge PR #1 в lab-base): https://github.com/doozdogg-jpg/test1/actions/runs/37996724925
- Оба job зелёные, Run tests: 8 методов, OK.

## Два новых сценария
- test_negative_elapsed: is_overdue(-1) -> ValueError.
- test_unknown_priority: is_overdue(10, "urgent") -> ValueError (urgent не входит в high/normal/low).

## Что автоматическая проверка пока не покрывает
- Входные типы кроме числа и строки (None, строка вместо времени, float/NaN).
- Версии Python вне матрицы (3.11, 3.12) и другие ОС.
- Нет обязательных checks в правилах ветки main: красный check сам по себе не блокирует merge.