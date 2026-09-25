# Live Face Mask

A face is replaced in the webcam feed while it is being filmed, and the finished frame goes
straight into OBS or a call. Written in C++ and running on the GPU, because at 30 frames a
second there is a 33-millisecond budget for everything: find the face, place 106 points on
it, cut the mask, generate the new face, blend it in.

> [Русская версия](live-face-mask.ru.md) · [← back to portfolio](../README.md)

[▶ 30-second clip](../media/video/live-face-mask.mp4)

*(Interface screenshots are coming — the app opens a window on the machine it runs on, and
the screen was busy when this page was written.)*

---

## Every frame passes through five networks

| Stage | Model | Job |
|---|---|---|
| Detection | `det_10g` | where the faces are |
| Landmarks | `2d106det` | 106 points: face contour, eyes, brows, nose, mouth |
| Identity | `w600k_r50` | the source face turned into a vector the generator can use |
| Generation | `inswapper_128` | the new face |
| Parsing | `face_parsing_bisenet` | which pixels are skin, which are hair, eyes, mouth — so the mask follows the face and not an oval |

All of it in ONNX Runtime on CUDA, the frame stays on the GPU between stages, and the window
shows the cost of each one separately: network, generator, landmarks, mask, compositing.

---

## The window

**Source face** — pick an image. Several angles of the same person can be blended, with a
slider for how much: a single frontal photo falls apart when the head turns.

**Camera** — device, resolution, frame rate, mirror. Two switches decide the method:
*neural replacement* (the generator) and *mask by landmarks* (without it the mask falls back
to an ellipse). *Show 106 points* draws the landmarks over the frame — that is how you see
whether the app has lost the face, rather than guessing from a bad result. A line below shows
the **actual camera mode**: if the camera handed over YUY2 instead of MJPG, that alone
explains a low frame rate, and the app says so instead of leaving you to wonder.

**Hardware** — which GPU was found and what the computation actually runs on.

**Mask settings**, all live while the video runs:

| Slider | What it changes | Why it is there |
|---|---|---|
| Blend | how strongly the new face replaces the old | |
| Mask strength | how much of the area the mask covers | |
| Frame light | matches the new face to the lighting of the room | a face lit differently from the scene reads as fake instantly |
| Mask smoothing | how soft the mask edge is | the app warns: above 70 the mask visibly lags behind the face; with the generator on, above 60 it eats the resemblance |
| Hair colour | tints the hair towards the source photo | disabled with a reason when no hair was found in the photo |
| Scale, offset X/Y | fits the mask to a face of a different shape | |
| Keep mouth / keep eyes | leaves your own mouth or eyes untouched | speech and gaze are what betray a swap; the original mouth keeps lip-sync exact |

**Live counters**: frames per second, latency, and the milliseconds spent by the network, the
generator, the landmarks, the mask and the compositing — plus head rotation (it says
*profile* when the face has turned away), the number of faces found and the number of frames
dropped.

---

## Getting it into a stream

A checkbox opens a second window, `Live Face Mask Output`, holding nothing but the finished
frame — no interface, no debug overlays. OBS takes it as a window capture, client area,
1280×720, with no scaling. Captured that way the window keeps working while minimised or
covered by other windows, which matters: streamers do not have a spare monitor for it.

Window capture adds one or two frames. **Camera to stream is about 100 ms** in total. The
right answer is a virtual camera — a DirectShow filter registered in the system, so the app
appears in the camera list next to the webcam; that is the next piece of work, and window
capture stays as the fallback.

---

## Where this must not go

The output frame carries the line **"AI-generated face effect"**, burned in last, over
everything, with no way to switch it off. Streaming platforms strip metadata; a line in the
picture survives re-encoding, download and re-upload.

The app is for streaming and recording — a face you present on camera without showing your
own. It is not built for identity documents, not for video calls where the other side
believes they are looking at a specific person, and the permanent label is there so the
result cannot quietly pass for genuine footage.

| | |
|---|---|
| Language | C++17 |
| Inference | ONNX Runtime, CUDA, cuDNN |
| Graphics | Direct3D 11 |
| Interface | Dear ImGui |
| Camera | Media Foundation |
| Build | CMake, MSVC |
| Output | OBS window capture; virtual camera planned |
