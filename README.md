# Oleksandr Voznyi — AI Product Developer

Romania / Ukraine · open to remote, UK, USA, Europe

Four working products: a desktop trading journal, a real-time speech-to-speech interpreter,
a real-time face replacement for streaming, and a multi-model Telegram assistant. All of them
run — the screenshots and clips here come from the running apps, not from mockups.

> **Language:** English · [Русская версия](README.ru.md)

| Project | What it is | Built with | Status |
|---|---|---|---|
| [**Trading Journal**](projects/trade-journal.md) | Desktop journal for futures traders: every trade in ticks, statistics and charts drawn from them | Python (stdlib only), SQLite, WebView2, single `.exe` | In daily use on a live account |
| [**AI Voice Interpreter**](projects/ai-voice-interpreter.md) | Two people talk in two languages: you speak Russian, they hear English — 275–520 ms behind you | Python, Whisper, Argos MT, Piper/Kokoro TTS, CUDA | Core done, 253 tests |
| [**Live Face Mask**](projects/live-face-mask.md) | Face replaced on the webcam feed in real time, straight into OBS or a call | C++17, ONNX Runtime + CUDA, Direct3D 11, Dear ImGui | Working, ~100 ms camera to stream |
| [**AI Telegram Bot**](projects/ai-telegram-bot.md) | One chat, 18 AI models, documents, images, voice, indicator building, subscriptions | Python, aiogram, PostgreSQL, Docker | In production, 312 tests |

## Watch them run

| | |
|---|---|
| [Trading Journal](media/video/trade-journal.mp4) | calendar, statistics, how a trade is entered |
| [AI Voice Interpreter](media/video/voice-interpreter.mp4) | the path from microphone to translated voice |
| [Live Face Mask](media/video/live-face-mask.mp4) | detection, landmarks, swap, OBS output |

GitHub does not play `.mp4` inline — click a link to download or open the clip.

## Why there is no source code here

These are products, not exercises. Two of them earn money, one handles a live trading
account, and each took months to build. This repository shows **what exists and that it
works**; the code stays private.

Happy to walk through any part of it in a call or a live demo, and to share a specific module
on request.

## How these were built

I design the product, decide how it must behave, test every build against real use, and work
with an AI assistant on the implementation. Requirements, architecture decisions, acceptance
and the bug hunting are mine; a large share of the typing is not. It is a fast way to build,
and I would rather say so plainly than let anyone assume otherwise.

What that leaves me good at: turning a vague idea into a precise specification, finding the
case where the thing breaks, and shipping something a real person uses the next morning.

Three habits visible in all four projects:

- **Nothing pretends to work.** Unavailable features are shown disabled with the reason next
  to them — there are no buttons that look alive and do nothing.
- **Honest numbers.** Latency is measured, not estimated; the translation quality figure
  quoted is the one from the held-out set, not the set the rules were tuned on.
- **The failure is named.** When the camera hands over the wrong pixel format, the app says
  so instead of leaving you with a low frame rate and no explanation.

## Contact

**Oleksandr Voznyi** · Romania / Ukraine

- Email — [aleksandr.vzn@icloud.com](mailto:aleksandr.vzn@icloud.com)
- Telegram — [@s1804v](https://t.me/s1804v)
- GitHub — [OVALLProjects](https://github.com/OVALLProjects)

Open to a call in English or Russian. Happy to demo any of these live.
