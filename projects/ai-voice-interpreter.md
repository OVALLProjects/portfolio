# AI Voice Interpreter

Two people talk, each in their own language. You speak Russian, the other side hears English;
they answer in English, you hear Russian. It appears in Windows as an ordinary audio device,
so it works inside Zoom, Discord, Google Meet — anywhere you can pick a microphone.

> [Русская версия](ai-voice-interpreter.ru.md) · [← back to portfolio](../README.md)

[▶ 30-second clip](../media/video/voice-interpreter.mp4)

---

## The hard part

Everyone has seen speech translation. The difficulty is not the translation — it is the
**delay**, and what the translation does to the words that must not change.

**Delay.** Measured on an RTX 5060 Ti, models warm:

| Step | Time |
|---|---|
| Recognising a 3–5 s phrase (Whisper large-v3-turbo) | 160–300 ms |
| Translation (Argos) | 40–100 ms |
| Quality layer | ~1 ms |
| Speech synthesis to first sound | 55–135 ms |
| **End of your phrase → first sound of the translation** | **275–520 ms**, 413 ms average in a demo conversation |

The voice detector waits another 350 ms of silence before deciding the phrase ended. In
streaming mode translation starts before you finish: on a 9-second sentence the first sound
comes at 3.6 s instead of 9.6 s.

**What must not change.** A plain translator turns "Python" into a snake, "Apple" into fruit,
mangles version numbers and drops the negation out of a sentence. A dedicated layer protects
terms, brand names, code, numbers, dates and addresses, resolves the snake-or-language cases
from context, handles idioms and slang, repairs what speech recognition mishears ("си плюс
плюс" → C++), and then checks the result against the original for numbers, negations and
question form. If the check fails, the phrase is marked *low confidence* instead of being
delivered as if it were fine.

Measured on a **held-out set of 30 phrases the rules were never tuned on: 70% → 97%**. On the
main 105-phrase suite it is 59% → 100%, but that is the set the rules were written against,
so the held-out number is the honest one.

---

## The window

![Settings before a call](../media/ai-voice-interpreter/01-window.png)

*(Taken from the running app — the device lists are the ones it actually found.)*

**Conversation** — your language and theirs, with a swap button between them. **You are /
The other person is** sets grammatical gender: Russian marks the speaker's gender in almost
every sentence about themselves ("I was late" is *опоздал* or *опоздала*) and English does
not carry it, so without this the translator has to guess. **Domain** picks the field whose
terms get protected — 17 of them, plus an interview mode — and can be changed mid-call.

**Audio** — microphone, headphones, which virtual cable the translation is sent to, and where
the other person's voice comes from. **Their original voice** is a slider: you keep hearing
their tone quietly under the translation, and it ducks while the translation plays. At zero
you hear only the translation.

**Voice & behaviour** — voice and speed, natural voices per language (more human, ~0.3–0.5 s
slower to start), and a latency slider from *fast answer* to *wait for the end of the
phrase*. Then the switches that decide how it behaves in a real conversation: translate while
speaking, translate the incoming voice, stop the translation when someone starts talking
(for speakers; leave off with headphones), show the transcript, push-to-talk on Space, echo
guard, automatic language detection.

**Start interpreter** is the only button that matters. Anything unavailable is shown
**disabled with the reason next to it** — there are no buttons that look alive and do
nothing.

---

## Audio routing, which is where these things usually die

- The translation is sent into a **virtual cable** and picked in Zoom as the microphone.
  Verified on a live VB-Cable: the translation was recorded from CABLE Output and recognised
  again at 96% of words.
- The other side's voice can be captured **without any driver**: route the call's playback to
  a free output and capture it as loopback. Verified on a device.
- Routes are checked against feedback loops before the call, and there is a self-test that
  says which route is wrong rather than leaving you with silence.

---

## Vocabulary — the words a translator must not improvise on

![Vocabulary tab](../media/ai-voice-interpreter/02-vocabulary.png)

A term is either **kept as is** — product names, tool names, anything that has no business
being translated — or **translated one fixed way**, so the same word never comes out
differently in the middle of a call. Each entry can be bound to a domain, carry its
inflected forms, and fire only when certain words are nearby: *Python* is the language next
to *code* or *script*, and the snake everywhere else. Import and export are JSON and CSV, so
a glossary can be prepared outside the app.

*(The terms in the shot are made up for the screenshot.)*

---

## Setup and privacy

![Setup and privacy tab](../media/ai-voice-interpreter/03-privacy.png)

The **audio routing check** takes about eight seconds: it sends test tones into the virtual
cable and a beep into the headphones, and tells you which leg of the route is wrong instead
of leaving you in silence during the call.

Privacy is a set of switches, all off unless turned on: audio is not written to disk, the
transcript stays in memory, the local engine sends nothing over the network, and voice
cloning requires explicit consent and can be deleted. **Delete history** removes everything
at once.

---

## Also in it

Conversation memory and speaker profile, in memory only, never written to disk · Natural /
Literal styles locally · minute and character accounting with Free / Pro / Business tiers ·
a developer panel with per-stage latency, quality metrics and a phrase breakdown.

Privacy: the whole pipeline — recognition, translation, synthesis — runs locally on the
machine. Cloud models are optional and off by default.

---

## Honest status

Working and tested: both directions, streaming, the quality layer, routing, the window,
interruption, echo guard, mute, push-to-talk. **253 tests.**

Not done: cloud speech-to-speech engines, diarisation and crosstalk, noise suppression and
echo cancellation (audio quality is only measured, not improved), voice matching. A real call
with a live partner on the other end is still ahead — everything so far was verified with
recordings and loopback.

| | |
|---|---|
| Language | Python 3.12, Windows 10/11 |
| Recognition | Whisper large-v3-turbo |
| Translation | Argos (local), optional LLM for style rewriting |
| Speech | Piper, Kokoro, Vosk |
| Acceleration | CUDA; runs on CPU about 14× slower |
| UI | pywebview, one HTML page |
| Packaging | a portable build that runs without Python installed |
