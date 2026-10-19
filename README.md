# Pace Maker v.12

Native HTTP load testing for macOS — fire configurable traffic at any endpoint and watch it respond in real time.

![Pace Maker running a load test: live chart with the settings inspector open](images/main_window.png)

## What is Pace Maker?

Pace Maker is a lightweight, fully native macOS app for putting HTTP endpoints under load. Point it at a URL, set how hard you want to push, and hit **Start**. Requests fire at your target rate while a live chart plots throughput, successes, failures, and error rate second by second. No browser tab, no YAML config, no Docker — just a clean desktop tool that does one thing well.

Built entirely in **Swift and SwiftUI** with structured concurrency under the hood, Pace Maker runs as a real macOS app, not a cross-platform wrapper.

## Why Pace Maker?

- **Real-time visibility.** A live Swift Charts graph shows sent vs. received throughput, failures, and error rate as the test runs — not a summary report after the fact.
- **Honest backpressure.** When your target is faster than the endpoint can answer, Pace Maker _shows_ the achieved rate dropping instead of silently buffering forever. That dip is the signal you actually want.
- **Inspect every exchange.** A built-in request/response viewer shows exactly what's going over the wire, with sliding-window history and optional truncation.
- **Native feel.** A Liquid Glass toolbar, a settings inspector, keyboard shortcuts, header autocomplete, and settings that are remembered between launches. It behaves like a Mac app because it is one.

## Key Features

### 📈 Live chart — four series, fully interactive

The chart plots **Sent**, **Received**, **Failures**, and **Error Rate** as separate series. Click any legend pill to toggle a series on or off, drag the chart to pan back through time, and set the visible window from 5 seconds up to 5 minutes. The window subtitle keeps the run state, elapsed time, and sent count in view.

![Live chart of sent, received and failed requests per second](images/chart.png)

### ⚙️ Settings inspector — dial in exactly how hard to push

Every setting lives in one native inspector beside the chart: the target URL, transport and User-Agent preset; the total **target RPS**, maximum connections and backpressure queue depth; and stop conditions — total requests, a duration, or both. Toggle it from the toolbar; your last configuration is remembered between launches.

<img src="images/settings.png" alt="Settings inspector with Target, Load and Stop When sections" width="322">

### 📊 Stats — the numbers that matter

A grid of live stat cards: elapsed time, sent, succeeded, failed, and shed requests; live vs. maximum connections; current and average RPS; average latency; backpressure queue depth; and your configured target.

![Stats view with live counters](images/stats.png)

### 🔍 Request & response viewer

See the exact request being fired — method, path, headers, and User-Agent — and inspect live responses as they come back, status line and headers included. Toggle **Sliding** to keep a rolling window of the most recent responses, cap how many you retain, and turn on **Truncated** to collapse long bodies.

![Texts view showing the request and recent responses](images/texts.png)

### 🧩 Header editor with autocomplete

Add, edit, and remove request headers right in the inspector. Header-name fields autocomplete against the standard HTTP request header set as you type.

<img src="images/headers.png" alt="Header editor suggesting header names while typing" width="322">

### 🎛️ Full control, always one click away

Start, pause, resume, and reset from the window toolbar — or from the **Test** menu with **⌘R** and **⇧⌘R**. Pausing and then resuming continues with whatever time and requests are left. On macOS 26 and later the toolbar uses Liquid Glass, and on macOS 27 the view switcher uses the new tab style.

## How it works under the hood

Pace Maker is built on Swift structured concurrency end to end:

- A **token-bucket pacer** releases requests at exactly your target rate, never blocking on a response, into a bounded backlog served by a fixed pool of workers — nothing is spawned per request, so memory stays flat at any throughput.
- An **adaptive connection gate** sizes how many requests are in flight from observed latency and backs off when the server saturates. When every connection is busy, requests wait in the backlog; when the backlog is full, excess requests are **shed** rather than buffered indefinitely.
- Per-request outcomes are recorded into lock-protected counters without suspending, and a sampler reads them each sampling interval to drive the live chart and stats.

The result: when an endpoint can't keep up, you see the achieved RPS honestly drop below target instead of getting a misleading "everything's fine" reading.

## Roadmap

HTTP load testing ships today. The architecture is already protocol-agnostic, with these transports on the way:

- **gRPC** endpoint testing
- **WebSocket** load
- **Raw TCP** load

## Requirements

- **macOS 14** or later (Liquid Glass on macOS 26+, tab-style view switcher on macOS 27)
- A Mac with **Apple Silicon** — release builds are arm64

## Getting started

1. [Download the latest release](https://github.com/eugenezimin/Pace-Maker/releases/latest) and open **Pace Maker**.
2. Enter a target URL in the settings inspector.
3. Set your target RPS and maximum connections.
4. Choose a stop condition — total requests, a duration, or both.
5. Press **Start** (⌘R) and watch the chart.

## About

Pace Maker is created by **Eugene Zimin**. 🔗 [github.com/eugenezimin](https://github.com/eugenezimin)

<div align="center">

© 2026 Eugene Zimin. All rights reserved.

</div>
