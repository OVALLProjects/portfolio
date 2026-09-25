<img src="media/banner.png" alt="Oleksandr Voznyi — AI Product Developer, AI Solutions Engineer, AI Automation Specialist">

### [▶ Open the live portfolio — films play right on the page](https://ovallprojects.github.io/portfolio/)

**English** · [Русская версия](README.ru.md)

---

Four projects in the last year and a half, alone, in four different technical areas —
specification, architecture, implementation. One runs on a **live trading account** every day,
one starts translating **before the speaker finishes the sentence**, one replaces a face on a
live stream in **~100 ms**, and one puts **18 models** behind a single chat window. All four are
finished and working.

Every clip and screenshot here comes from the running application. Each section below gives the
decision that made the thing work, and what that decision bought.

| | |
|---|---|
| **4** | finished products, all of them working |
| **565** | automated tests across two of them |
| **18** | AI models behind one chat window |
| **275 ms** | fastest measured speech-to-speech |

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

**The decision — one file, nothing installed.** The interface is HTML drawn by WebView2, which
Windows already has; underneath it is the Python standard library and SQLite, with no framework
and no third-party package. *What it gave:* the journal ships as a single 13.6 MB executable that
opens on a machine with nothing prepared, and there is no dependency to repair a year later.

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

**The decision — a second pass over the translation.** Raw machine translation was right about
70% of the time on my test material. Behind it sits a quality layer: a glossary of terms that
must not drift, corrections for the constructions the model reliably breaks, and a check that
what came out is a usable sentence at all. *What it gave:* **97%** on a held-out set the rules
were never written against — the figure worth quoting, rather than the higher one from the
material they were tuned on.

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

**The decision — cut along the face, not along an ellipse.** The generated face is blended
through the parsing map computed for that same frame, so hair, glasses and a hand crossing the
cheek stay where they are, and the original mouth and eyes can be kept untouched. *What it gave:*
lip-sync stays exact, because the mouth on screen is the real one — the detail that gives a swap
away before anything else does.

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

**The decision — four layers, so a change lands in one.** Telegram, business logic, providers and
storage are separate, with the boundaries held to. *What it gave:* a new model is a change in the
provider layer, a new interface language a change in presentation, and eighteen providers
accumulated over time without the code turning into a knot.

- Produces real `.pptx` decks and `.docx` papers in 11 regional formatting standards
- Builds trading indicators for 7 platforms, compiling `.jar` studies server-side
- **312 tests**, one suite dedicated purely to pricing mistakes that cost money
- Voice messages reach the model as audio, with no transcription step in between

`Python · aiogram`  `PostgreSQL · Alembic`  `Docker Compose`

[Every capability and layer →](projects/ai-telegram-bot.md)

---

## How I work

A desktop application, a real-time GPU pipeline, local speech AI and a service over eighteen
model providers share no code and barely any vocabulary. What they do share is how each one got
decided, built and checked — and that is the part that transfers to whatever a company happens to
be building.

- **It starts as a specification.** What the thing must do, who is holding it, and what would
  make it useless — written down before any architecture exists. The arguments that otherwise
  arrive in week six happen on page one, when changing your mind is free.
- **The architecture answers one constraint.** Each product has a single constraint that decides
  its shape: 33 ms per frame, a trader who will not install a runtime, a streamer with no spare
  monitor for the output window. I find the one that actually binds and build outward from it.
- **Numbers come from measurement.** Latency is broken down by stage and displayed while the
  thing runs; accuracy is read off material the rules never saw. Any figure I cannot reproduce on
  demand does not go into this repository or into a conversation.
- **Failure is named, not hidden.** A feature that cannot work is shown disabled with the reason
  beside it. When a camera hands over the wrong pixel format the application says so, rather than
  leaving someone with a bad frame rate and a guess.
- **The implementation is written with an AI coding tool.** I set the requirements, decide how the
  product behaves, make the architecture calls and test every build against real use; the code is
  written alongside an AI assistant. That is the reason one person covers four technical areas in
  eighteen months, and it is the same leverage I would bring to a team.

**What is here, and what is not.** The repositories stay private: this is the evidence, not the
source. I will walk through any part of the code on a call, run any of it live in front of you,
or send a specific module across.

---

## What comes next

A project reaches this page once it is finished and working, so the page fills up slowly and
everything on it is complete. The work did not stop at four.

- **Finished, working, and still developing.** All four are built, tested and working — the
  journal on a live trading account every trading day. Each keeps developing after release: the
  journal gains a breakdown when a month of trading asks for one, the bot gains models as
  providers publish them, and the mask is moving from window capture to a proper virtual camera.
- **The ones after these.** Further products are being built now, and they arrive here on the
  same terms as these four: finished, measured and working. This page gains completed products,
  not announcements.
- **What I am looking for.** A role where this goes into a company's product: AI features,
  agents, voice, automation — the distance between a requirement that is still vague and
  something a customer opens. Remote, full-time or contract, UK, USA or Europe. I read a domain
  quickly and I would rather own an outcome than a ticket queue.

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
