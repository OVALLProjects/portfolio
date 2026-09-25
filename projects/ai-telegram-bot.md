# AI Telegram Bot

One chat with 18 AI models behind it. You do not pick a specialisation and you do not learn
commands: `/start` → language → model → question. The bot works out what is being asked and
adapts, including when the subject changes mid-conversation from trading to recipes and back.

> [Русская версия](ai-telegram-bot.ru.md) · [← back to portfolio](../README.md)

---

## What a user can do with it

| | |
|---|---|
| **18 models** | GPT, Claude, Gemini, DeepSeek, Qwen, Llama 4, GLM, and 8 free ones |
| **🎯 Auto** | picks the model for the specific question |
| **Answer as it happens** | the text grows inside the message while it is generated, instead of silence and then a wall |
| **Vision** | photos, screenshots, scans, albums: reads text, reads charts, reads someone else's code |
| **Hearing** | voice messages and audio go to the model as they are, with no transcription step in between |
| **Video** | video and GIFs go to the model whole |
| **Documents** | PDF, docx, xlsx, csv, source code — complete, with their layout and tables |
| **Links** | send a URL and the bot reads the page and answers from it |
| **Photo editing** | "remove the background", "make it anime" — a new image comes back |
| **Search** | questions about today, rates and news go to the internet, and the answer carries links |
| **Images** | on request; simple ones free, detailed briefs on a paid model |
| **Presentations** | PDF plus a real `.pptx`: varied slide layouts, illustrations, author name, speaker notes |
| **Academic papers** | PDF plus `.docx` by chapters, 11 formatting standards across CIS and Europe |
| **Tables** | markdown tables become real ones: a grid in Word and PDF, columns in the chat |
| **Files** | code and spreadsheets arrive as attachments with the right extension |
| **Trading indicators** | TradingView, MetaTrader 4/5, MotiveWave, NinjaTrader, cTrader, Quantower, Sierra Chart |
| **`.jar` builds** | MotiveWave studies are compiled on the server, and build errors are fixed automatically |
| **Self-tuning** | user ratings change which model gets chosen |
| **Subscriptions** | a 30-day package without daily limits, paid by transfer or Telegram Stars |
| **Languages** | Russian, English, Ukrainian |

---

## How it is put together

| Layer | Responsible for |
|---|---|
| Handlers | Telegram only: what was pressed, what to show |
| Views | rendering, shared between commands and buttons |
| Catalogue | platforms, models, system prompt |
| Intent | image, presentation, file, complexity |
| Attachments | photos, voice and documents coming in |
| Routing | subject of the question and the order of models to try |
| Plans | limits, prices, unit economics |
| Services | AI, images, builds, quality, subscriptions, payments |
| Providers | wrappers over the APIs behind one interface |
| Database | models and queries |
| Admin API | statistics and control over HTTP |

Telegram, business logic, AI providers and the database do not mix: a handler does not know
which SDK is being called, and a provider knows nothing about Telegram. Adding a model,
a provider, a language or a tariff touches one layer.

**312 tests**, none of them needing the network. They cover what breaks silently: intent
detection, tariff economics, Telegram's own limits, completeness of translations. One suite
exists purely to catch mistakes that cost money — margin gone negative, a cheaper plan that
beats a more expensive one, more images promised than the limit allows — and it runs on every
price change.

---

## Two decisions worth naming

**Official APIs only.** The plan names contain "Pro", but no ChatGPT Plus / Claude Pro
account, cookie or session token is used anywhere. That route breaks the providers' terms and
ends in a ban; paid API keys do not.

**A missing key hides a feature instead of breaking it.** The bot starts with a single
provider key — the free Gemini one is enough — and every platform without a key simply does
not appear in the menu. No dead buttons.

| | |
|---|---|
| Language | Python |
| Bot framework | aiogram |
| Database | PostgreSQL, migrations with Alembic |
| Deployment | Docker Compose, one command |
| Admin | HTTP API plus in-chat commands |
