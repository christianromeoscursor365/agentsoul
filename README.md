# AgentSoul

Дайте OpenCode душу — внедрение личности и долговременной памяти для вашего AI-агента.

## Установка

```bash
npm install -g @neomei/agentsoul
```

Если после установки появляется ошибка `command not found: agentsoul`, значит глобальный каталог npm не в PATH. Настройте его:

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

Или запустите через `npx` без глобальной установки:

```bash
npx @neomei/agentsoul setup
npx @neomei/agentsoul chat
```

## Быстрый старт

```bash
# 1. Настройте личность агента (плагин регистрируется автоматически)
agentsoul setup

# 2. Запустите OpenCode с внедрением личности
agentsoul chat
```

## Команды

| Команда | Описание |
|---------|----------|
| `agentsoul setup` | Интерактивный мастер настройки (автоматически регистрирует плагин) |
| `agentsoul uninstall` | Удалить плагин из конфига opencode |
| `agentsoul chat` | Запустить OpenCode TUI с внедрением личности |
| `agentsoul run <сообщение>` | Одноразовый запуск с внедрением личности |
| `agentsoul serve [порт]` | Безголово́сный сервер с внедрением личности |
| `agentsoul memory list` | Показать недавние диалоги |
| `agentsoul memory search <запрос>` | Поиск по истории диалогов |
| `agentsoul memory clear` | Очистить всю память |

## Как это работает

AgentSoul внедряет файлы личности в каждую сессию OpenCode:

1. **Файлы души** (`~/.agentsoul/soul/`)
   - `IDENTITY.md` — кто ваш агент
   - `SOUL.md` — базовые принципы и стиль общения
   - `USER.md` — кто вы и ваши отношения

2. **Память** (`~/.agentsoul/memory.db`)
   - Хранилище истории диалогов на SQLite
   - Недавние записи автоматически подставляются в новые сессии

3. **Плагин OpenCode** — регистрируется в `~/.config/opencode/opencode.json`
   - Подключается к `experimental.chat.system.transform` для внедрения личности перед каждым вызовом LLM
   - Подключается к `chat.message` для сохранения сообщений пользователя
   - Переживает сжатие контекста автоматически

## Настройка

После `agentsoul setup` редактируйте файлы напрямую:

```bash
~/.agentsoul/soul/IDENTITY.md   # Имя, возраст, личность
~/.agentsoul/soul/SOUL.md       # Базовые принципы, стиль общения
~/.agentsoul/soul/USER.md       # Отношения с пользователем
```

## Интеграция с OpenCode Feishu

AgentSoul работает с [@neomei/opencode-feishu](https://www.npmjs.com/package/@neomei/opencode-feishu), чтобы дать душу вашему боту в Feishu.

**Требование**: оба плагина должны быть зарегистрированы в `~/.config/opencode/opencode.json`:

```json
{
  "plugin": [
    "@neomei/agentsoul",
    "@neomei/opencode-feishu"
  ]
}
```

**Настройка**:

```bash
# 1. Настройте личность AgentSoul
agentsoul setup

# 2. Настройте подключение к Feishu
npx @neomei/opencode-feishu setup

# 3. Запустите OpenCode serve с обоими плагинами
opencode serve --port 19876
```

Когда пользователь Feishu отправляет сообщение, OpenCode обрабатывает его через оба плагина:

- **AgentSoul** внедряет личность в системный промпт
- **opencode-feishu** обрабатывает WebSocket Feishu и форматирование сообщений

Память (история диалогов) сохраняется автоматически.

## Архитектура

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   agentsoul     │────▶│   soul/*.md     │────▶│  stdin inject   │
│   CLI / Плагин  │     │  (личность)     │     │  (opencode run) │
└─────────────────┘     └─────────────────┘     └─────────────────┘
         │                                               │
         ▼                                               ▼
┌─────────────────┐                            ┌─────────────────┐
│  memory.db      │◀───────────────────────────│  opencode TUI   │
│  (SQLite)       │   сохранение диалогов      │  / serve        │
└─────────────────┘                            └─────────────────┘
```

## Лицензия

MIT
