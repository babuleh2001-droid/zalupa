# Telegram bot + Google Sheets

## 1) Что это за бот
Это шаблон Telegram-бота, который принимает команды от пользователя и работает с Google Sheets (чтение/запись данных).
Подходит как старт для учёта заявок, лидов, задач или простых CRM-сценариев.

---

## 2) Как создать Telegram-бота
1. Откройте Telegram и найдите `@BotFather`.
2. Отправьте команду `/newbot`.
3. Укажите имя и username бота.
4. Получите токен вида `123456789:ABC...`.
5. Сохраните токен — он нужен для `BOT_TOKEN`.

Проверка:
- откройте `https://t.me/<your_bot_username>`
- нажмите **Start**

---

## 3) Как заполнить `.env`
1. Скопируйте пример:
   ```bash
   cp .env.example .env
   ```
2. Откройте `.env` и заполните значения:
   - `BOT_TOKEN` — токен от BotFather
   - `ADMIN_CHAT_ID` — ваш Telegram chat_id (для уведомлений)
   - `GOOGLE_CREDENTIALS_PATH` — путь к JSON ключу service account
   - `GOOGLE_SHEET_ID` — ID таблицы из URL Google Sheets
   - `GOOGLE_WORKSHEET_NAME` — имя листа внутри таблицы (например `Sheet1`)
   - `TZ` — часовой пояс
   - `LOG_LEVEL` — уровень логирования (`INFO`, `DEBUG`, ...)

Пример URL таблицы:
`https://docs.google.com/spreadsheets/d/<GOOGLE_SHEET_ID>/edit`

---

## 4) Как установить зависимости
### Linux / VPS
```bash
sudo apt update
sudo apt install -y python3 python3-venv python3-pip

python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

---

## 5) Как запустить локально
> Ниже команда для типового входа в проект. Замените `bot.py` на ваш фактический entrypoint (например `main.py`), если он отличается.

```bash
source .venv/bin/activate
python bot.py
```

Если entrypoint другой:
```bash
python main.py
```

---

## 6) Как запустить на VPS
Надёжный вариант — через `systemd`.

1. Создайте юнит:
```bash
sudo nano /etc/systemd/system/telegram-bot.service
```

2. Вставьте (подставьте пути и пользователя):
```ini
[Unit]
Description=Telegram Bot
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/opt/telegram-bot
Environment="PYTHONUNBUFFERED=1"
ExecStart=/opt/telegram-bot/.venv/bin/python /opt/telegram-bot/bot.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

3. Примените и запустите:
```bash
sudo systemctl daemon-reload
sudo systemctl enable telegram-bot
sudo systemctl start telegram-bot
sudo systemctl status telegram-bot --no-pager
```

4. Логи:
```bash
sudo journalctl -u telegram-bot -f
```

---

## 7) Как подключить Google Sheets credentials
1. Откройте [Google Cloud Console](https://console.cloud.google.com/).
2. Создайте проект (или выберите существующий).
3. Включите Google Sheets API (и при необходимости Google Drive API).
4. Создайте **Service Account**.
5. Создайте JSON key и скачайте файл.
6. Положите файл в проект, например:
   ```bash
   mkdir -p credentials
   mv ~/Downloads/your-key.json credentials/service-account.json
   ```
7. Укажите путь в `.env`:
   ```env
   GOOGLE_CREDENTIALS_PATH=credentials/service-account.json
   ```
8. Откройте Google Sheets и выдайте доступ service account email (Editor).

---

## 8) Основные команды и сценарии
Базовые команды (типовой набор):
- `/start` — приветствие и проверка, что бот работает.
- `/help` — список команд.
- `/ping` — health-check.

Типовые сценарии:
1. Пользователь отправляет данные (например заявку).
2. Бот валидирует формат.
3. Бот записывает строку в Google Sheets.
4. Бот подтверждает пользователю результат.
5. При ошибке отправляет сообщение админу (`ADMIN_CHAT_ID`).

---

## 9) Что планируется во V2
- FSM-анкеты (пошаговые формы) с валидацией.
- Роли пользователей (админ/менеджер/пользователь).
- Инлайн-кнопки и меню.
- Экспорт в CSV/Excel.
- Метрики и алерты по ошибкам.
- Docker + `docker compose` для развёртывания.
- Тесты (unit/integration) и CI.

---

## Быстрая проверка после запуска
1. Напишите боту `/start`.
2. Убедитесь, что нет ошибок в логах.
3. Отправьте тестовые данные.
4. Проверьте, что новая строка появилась в Google Sheets.
