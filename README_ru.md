# Agent Graph Task

[English](README.md) · [Руководство](https://falkolab.github.io/agent-graph-task/guide.ru.html) · [English guide](https://falkolab.github.io/agent-graph-task/guide.en.html)

Протокол выполнения сложных задач AI-агентами: граф ограниченных этапов, явные критерии приёмки, независимые проверки, контролируемые исправления, учёт ресурсов и продолжение работы между сессиями.

**Текущая реализация:** Skill для Claude Code. Один протокол поддерживает прямое исполнение и, когда это оправдано, Claude Code Dynamic Workflows.

## Установка

**Для одного проекта** (из корневой папки):

```bash
git clone https://github.com/falkolab/agent-graph-task.git /tmp/agent-graph-task
mkdir -p .claude/skills
cp -R /tmp/agent-graph-task/.claude/skills/graph-task .claude/skills/
```

**Глобально** (во всех проектах Claude Code):

```bash
git clone https://github.com/falkolab/agent-graph-task.git /tmp/agent-graph-task
mkdir -p ~/.claude/skills
cp -R /tmp/agent-graph-task/.claude/skills/graph-task ~/.claude/skills/
```

Вызов Skill в Claude Code:

```text
/graph-task Выполни рефакторинг импортера без изменения внешнего поведения. Перед изменениями предложи граф задач и критерии приёмки.
```

Skill определяет задачи, проверки, границы изменений и бюджет до начала исполнения. Узлы можно выполнять напрямую или через Dynamic Workflows. Запуск Workflow требует отдельного согласования. Advisor не обязателен и настраивается независимо.

## Как работает

- **Планирование:** узлы, зависимости, входы, выходы, исполнители и критерии приёмки фиксируются в `graph.md`.
- **Исполнение:** детерминированные операции выполняются кодом, независимые задачи делегируются при необходимости. Dynamic Workflow — альтернативный механизм исполнения, а не второй граф задач.
- **Проверка:** результат принимается после проверки сохранённого артефакта и версий входных данных; доказательства записываются в `checks.md`.
- **Продолжение:** состояние сохраняется в `progress.md`, перед возобновлением проверяется актуальность результатов.
- **Контроль:** соблюдаются согласованные полномочия, лимиты исправлений и учёт ресурсов.

Файлы `graph.md`, `checks.md` и `progress.md` создаются для выполняемых задач и не поставляются как пустые шаблоны.

## Состав

| Файл | Назначение |
| --- | --- |
| [SKILL.md](.claude/skills/graph-task/SKILL.md) | Точка входа и инструкции Skill |
| [references/protocol.md](.claude/skills/graph-task/references/protocol.md) | Полный нормативный протокол |
| [Руководство на русском](https://falkolab.github.io/agent-graph-task/guide.ru.html) | Пояснения, схемы и примеры |
| [English guide](https://falkolab.github.io/agent-graph-task/guide.en.html) | Английская версия руководства |

HTML-руководства предназначены для человека. Агент следует `SKILL.md` и `protocol.md`.

## Связанный проект

[AI Agent Task Graph — универсальный промпт](https://github.com/falkolab/agent-task-graph-prompt) не привязан к конкретному агентному инструменту. В этом репозитории размещена реализация для Claude Code.

## Автор

Андрей Ткаченко, телеграм-канал «Автоматизируй это»: [@aitomateit](https://t.me/aitomateit)
