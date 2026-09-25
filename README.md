<img src="media/banner.png" alt="Oleksandr Voznyi — AI Product Developer, AI Solutions Engineer, AI Automation Specialist">

### [▶ Open the live portfolio — films play right on the page](https://ovallprojects.github.io/portfolio/)

**English** · [Русская версия](README.ru.md)

---

# Four products. Two months. One person.

About a year of learning, in which I released nothing. Then, by September 2026: a desktop trading
journal, a real-time speech-to-speech interpreter, a C++ face-replacement pipeline on the GPU,
and a Telegram assistant with **18 models** behind one chat window. Four technical areas, none of
which I had worked in before I started.

That rate is what I am offering. I take on volume and several things at once, I get into an
unfamiliar field quickly, and I take a product from a vague requirement to something a person can
open. All four are finished and working — every clip and screenshot here comes from the running
application.

| | |
|---|---|
| **565** | automated tests I wrote across two of them |
| **275 ms** | measured speech-to-speech, entirely on this machine |
| **~100 ms** | camera to stream, five networks per frame |
| **13.6 MB** | the whole journal — one file, nothing installed |

---

## What I bring

**Four technical areas. None of them familiar when I started.** A desktop application, a real-time
GPU pipeline, local speech AI and a service over eighteen model providers share no code and barely
any vocabulary. I had not written C++, touched CUDA or worked with speech models before this year.
What carries between them is how a product gets decided, built and checked — and that is the part
that transfers to whatever you are building.

- **I learn a field by releasing something in it.** Every one of these four was a field I entered
  by building a finished thing in it, not by reading about it first. Thirty-three milliseconds per
  frame teaches you more about GPU pipelines in a week than a course does in a term, because the
  deadline is real and the thing either runs at thirty frames a second or it does not. Hand me a
  domain I have never touched and the answer is the same shape: a specification in days, something
  openable in weeks.
- **What I want is volume.** Four products in two months is the rate I like working at, and the
  rate I am looking to keep: several things moving at once, decisions made quickly and revisited
  when the measurement says so. I would rather own an outcome end to end — what it must do, how it
  is built, whether it is good enough to release — than work through a queue of tickets someone
  else has cut.
- **It starts as a specification.** What the thing must do, who is holding it, and what would make
  it useless — written down before any architecture exists. The arguments that otherwise arrive in
  week six happen on page one, when changing your mind is free. Then the architecture answers one
  constraint: 33 ms a frame, a trader who will not install a runtime, a streamer with no spare
  monitor. I find the one that actually binds and build outward from it.
- **Numbers come from measurement.** Latency is broken down by stage and displayed while the thing
  runs. Accuracy is read off material the rules were never written against — which is why the
  interpreter is quoted at 97% and not at the higher figure from its own tuning set. Any number I
  cannot reproduce on demand does not go into this repository or into a conversation.
- **The implementation is written with an AI coding tool.** I set the requirements, decide how the
  product behaves, make the architecture calls and test every build against real use; the code is
  written alongside an AI assistant. That is why one person covers four technical areas at this
  pace, and it is the same leverage I would bring to a team.

**What is here, and what is not.** The repositories stay private: this is the evidence, not the
source. I will walk through any part of the code on a call, run any of it live in front of you, or
send a specific module across.

---

## Skills

| | |
|---|---|
| **Languages and platforms** | Python · asyncio · C++17 · CMake · SQL · HTML/CSS/JS · PowerShell · Git · REST APIs · Windows desktop · Linux · Docker |
| **AI in a running product** | 18 LLM providers · intent routing · prompt engineering · Whisper large-v3-turbo · ONNX Runtime · CUDA · cuDNN · Piper · Kokoro TTS · Argos MT · quality layers over model output |
| **Systems and delivery** | PostgreSQL · Alembic · SQLite · aiogram · Docker Compose · layered architecture · Direct3D 11 · Media Foundation · WebView2 · single-file builds |
| **Product work** | Specification · architecture under one constraint · latency measurement · automated testing · unit economics · EN/RU/UA interfaces · release and acceptance |

---

## 01 · Trading Journal

**A futures journal that survives changing the instrument.**
*In daily use on a live account.*

A desktop journal for futures trading: log the trade, read the month back, find out which setup
actually pays for itself. Six analytic views over one database.

https://github.com/user-attachments/assets/c8c2c66f-0786-4095-a8b1-83f5dfdb9a3f

**The decision — store distance, not money.** A trade is saved as a distance in ticks plus the
instrument it was taken on; dollars, R-multiples and every statistic are derived at read time
from that instrument's tick value and commission. *What it gave:* change the contract or the
position size and the whole history recalculates itself. A spreadsheet keeps showing the old
figures and never mentions it.

- One file — `TradeJournal.exe` at 13.6 MB. HTML drawn by WebView2, which Windows already has,
  over the Python standard library and SQLite: no framework, no third-party package, no installer,
  and no dependency to repair a year later
- 16 futures contracts preloaded with tick value and per-contract commission
- Nothing leaves the machine: `trades.db` sits next to the app, a backup is a file copy
- Interface in English and Russian, session-aware time zones

`Python · stdlib only`  `SQLite`  `WebView2`  `single-file build`

**[▶ Watch the full walkthrough](https://ovallprojects.github.io/portfolio/#journal)** · [Every control, explained →](projects/trade-journal.md)

<img src="media/trade-journal/01-month-en.png" width="49%"> <img src="media/trade-journal/02-all-time-en.png" width="49%">

---

## 02 · AI Voice Interpreter

**Two people, two languages, one conversation.**
*Working · 253 tests.*

You speak Russian and the other person hears English in a generated voice, both directions,
while the call keeps going. Recognition, translation and speech all run on this machine — the
conversation never leaves the room.

https://github.com/user-attachments/assets/a628b758-9aeb-4a88-9a93-1e32801685a7

**The decision — never wait for silence.** Audio is cut into phrases as it arrives instead of at
a pause, and a finished piece enters recognition while the next one is still being spoken.
*What it gave:* **275–520 ms** measured from the end of a phrase to the first translated sound,
because only the tail is still in flight. A system that waits for the full utterance pays for all
of it.

- A quality layer over raw machine translation — a glossary that must not drift, corrections for
  the constructions the model reliably breaks, a check that the output is a usable sentence at
  all: **70% → 97%** on a held-out set the rules were never written against
- Push-to-talk, mute and an echo guard, so it survives a real conversation
- Appears as an ordinary microphone inside Zoom, Discord and Meet
- No cloud, no account, no per-minute bill
- 253 automated tests

`Python`  `Whisper large-v3-turbo`  `Argos MT`  `Piper · Kokoro TTS`  `CUDA`

**[▶ Watch the full walkthrough](https://ovallprojects.github.io/portfolio/#interpreter)** · [Latency, quality and what is not done →](projects/ai-voice-interpreter.md)

<img src="media/ai-voice-interpreter/01-window.png" width="49%"> <img src="media/ai-voice-interpreter/02-vocabulary.png" width="49%">

---

## 03 · Live Face Mask

**Five neural networks per frame, inside 33 milliseconds.**
*Working · ~100 ms camera to stream.*

It replaces the face in a live webcam feed while the camera is running and hands the finished
picture to OBS as an ordinary window — no plugin, no virtual camera driver, nothing installed
into OBS. Detection, 106 landmarks, identity embedding, generation and face parsing happen on
every frame that passes through.

https://github.com/user-attachments/assets/9ea8aea5-e187-4448-9d36-d2464d272b23

**The decision — C++, and the frame never leaves the GPU.** Thirty frames a second leaves 33 ms
for all five networks, which ruled out Python early. The app is C++17 with ONNX Runtime on CUDA,
and a frame stays in video memory from capture through to compositing, with no copy back to the
CPU between stages; each stage reports its own milliseconds on screen. *What it gave:* **~100 ms**
from camera to stream — three frames at 30 fps — and a slow frame with a name rather than a
shrug.

- Blended through the face-parsing map computed for that same frame, not through an ellipse: hair,
  glasses and a hand crossing the cheek stay where they are, and the real mouth can be kept, so
  lip-sync stays exact — the detail that gives a swap away before anything else does
- 5 ONNX models, about 770 MB, resident on the GPU
- Per-stage latency shown live: network, generator, landmarks, mask, compositing

> **The label is burned into the picture.** "AI-generated face effect" goes on last, over
> everything, with no switch to remove it. Platforms strip metadata, so a line written into the
> pixels is the only kind that survives re-encoding, download and re-upload.

`C++17 · CMake`  `ONNX Runtime`  `CUDA · cuDNN`  `Direct3D 11`  `Dear ImGui`  `Media Foundation`

**[▶ Watch the full walkthrough](https://ovallprojects.github.io/portfolio/#face)** · [The pipeline, the sliders, the limits →](projects/live-face-mask.md)

---

## 04 · AI Telegram Bot

**One chat window, eighteen models behind it.**
*Live on Telegram.*

Send a photo, a voice message, a spreadsheet or a bare link, and whichever model suits that
question answers it — including when the subject changes mid-conversation and then changes back.
There is no mode to choose and no command to learn.

**The decision — the router picks the model, not the person.** Every message is read for intent
first (what kind of task, whether it needs vision, a long context or code), and that decides both
the subject and the order of models to try; ratings left on answers change which model wins next
time. *What it gave:* nobody has to know which of eighteen models is good at what, and the
nineteenth can be added without a single user changing a habit.

**The decision — official APIs, and the shortcut refused.** Consumer AI subscriptions can be
resold by driving them through captured browser sessions, and that is how a lot of these bots are
built. This one runs on the providers' paid APIs. *What it gave:* a service that still works the
week a provider tightens enforcement, instead of one that disappears along with the account it
was riding on.

- Four layers — Telegram, business logic, providers, storage — so a new model is a change in one
  place, a new interface language another, and the tariff arithmetic has a test suite of its own.
  Eighteen providers accumulated over time without the code turning into a knot
- A platform with no key never appears in the menu, instead of appearing and failing when pressed
- Produces real `.pptx` decks and `.docx` papers in 11 regional formatting standards
- Builds trading indicators for 7 platforms, compiling `.jar` studies server-side
- **312 tests**, one suite dedicated purely to pricing mistakes that cost money
- Voice messages reach the model as audio, with no transcription step in between

`Python · aiogram`  `PostgreSQL · Alembic`  `Docker Compose`

[Every capability and layer →](projects/ai-telegram-bot.md)

---

## What comes next

A product reaches this page once it is finished and working, so the page fills up slowly and
nothing on it is an announcement. All four keep developing after release: the journal gains a
breakdown when a month of trading asks for one, the bot gains models as providers publish them,
the mask is moving from window capture to a proper virtual camera. More are being built right now,
and they arrive here on the same terms — finished, measured, working.

**What I am looking for.** A role where this goes into a company's product: AI features, agents,
voice, automation. Remote, full-time or contract, UK, USA or Europe. If the stack is one I have
not used yet, that is the normal case here rather than the exception.

*Last updated: 25 September 2026. The commit history shows how often this repository changes.*

---

## Contact

**Oleksandr Voznyi** · Romania / Ukraine · open to remote, UK, USA, Europe

- Email — [aleksandr.vzn@icloud.com](mailto:aleksandr.vzn@icloud.com)
- Telegram — [@s1804v](https://t.me/s1804v)
- LinkedIn — [oleksandr-voznyi](https://www.linkedin.com/in/oleksandr-voznyi-763a41439/)
- GitHub — [OVALLProjects](https://github.com/OVALLProjects)

A call works in English or Russian. If you want to see something specific — the tick maths, the
latency breakdown, the tariff tests — say which, and I will open that part on screen.
