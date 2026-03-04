# Dev Environment Setup Prompt

Используй этот промпт в Claude Code для автоматической настройки dev-окружения.

---

## Промпт для установки

```
Ты — установщик dev-окружения. Работаешь в терминале на текущей машине.

Цель: подготовить окружение для Claude Code + MCP и skills:
- VPN (проверка, при необходимости попроси включить вручную)
- Homebrew
- Python
- Node.js
- Git
- GitHub CLI (gh) — ТОЛЬКО установить, НЕ логинить в GitHub и НЕ просить регистрацию
- Claude Code/CLI (claude)
- Авторизация в Claude
- MCP/плагины: Context7, Playwright, Serena (универсальный LSP)
- Skills: obra/superpowers

Правила:
1) Сначала сделай диагностику и выведи краткий план (что уже есть / чего нет).
2) Затем выполняй установку/настройку по шагам.
3) Перед изменением конфигов — делай бэкап файлов.
4) Где нужны ручные действия (VPN/логин в браузере) — остановись и попроси меня сделать действие.

ШАГ 0 — Диагностика
- Определи ОС/шелл:
  - uname -a
  - sw_vers (если macOS)
  - echo $SHELL
- Проверь VPN "по факту":
  - ifconfig | egrep "utun|ppp|tun" || true
  - (если есть) scutil --nc list || true
  - curl -s https://ifconfig.me ; echo
Если VPN не выглядит подключенным — попроси меня включить VPN вручную и подтвердить внешнюю IP.

ШАГ 1 — Homebrew
- Если brew нет: установи Homebrew официальным способом и добавь в PATH для zsh.
- Примечание: если запросит sudo — это пароль текущего пользователя macOS (пароль входа в систему).
- Затем:
  - brew update

ШАГ 2 — Базовые пакеты
- brew install python node git gh

ШАГ 3 — Claude Code
- npm install -g @anthropic-ai/claude-code
- Проверь:
  - claude --version
- Авторизация:
  - claude auth login
  (откроется браузер; дождись завершения)

ШАГ 4 — MCP/плагины: Context7, Playwright, Serena

A) Playwright MCP
- Установи как MCP сервер для Claude Code (user scope):
  - claude mcp add --scope user playwright -- npx -y @playwright/mcp@latest
- Проверь:
  - claude mcp list | grep -i playwright || true

B) Context7 MCP
- Установи Context7 MCP:
  - claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
- Альтернативный вариант (SSE transport):
  - claude mcp add --transport sse context7 https://mcp.context7.com/sse
- Проверь:
  - claude mcp list | grep -i context7 || true

C) Serena MCP (универсальный LSP)
- Требуется uv (если нет — brew install uv)
- Добавь Serena для Claude Code:
  - claude mcp add serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context claude-code --project "$(pwd)"
- Проверь:
  - claude mcp list | grep -i serena || true

ШАГ 5 — Skills: obra/superpowers
ВАЖНО: это ставится через plugin marketplace/команды в интерактивной сессии claude, не через brew.
- Запусти интерактивный `claude` и выполни:
  /plugin marketplace add obra/superpowers-marketplace
  /plugin install superpowers@superpowers-marketplace

ШАГ 6 — Финальный отчёт
Выведи единый статус:
- VPN (как проверялось)
- Установлено ли: brew, python3, node/npm, git, gh, claude
- Авторизация Claude: результат `claude auth status --text`
- MCP сервера Claude: вывод `claude mcp list` (с выделением context7/playwright/serena)
- Superpowers: какие команды выполнены/что осталось сделать
```

---

## Использование

1. Скопируй промпт выше
2. Открой терминал и запусти `claude`
3. Вставь промпт и следуй инструкциям
