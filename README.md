# Tadween

**A vendor-agnostic ASR pipeline ecosystem for high-throughput speech intelligence.**

Transcription, diarization, and summarization — decomposed into swappable, production-ready stages instead of one brittle, single-process script.

**Status:** 🚧 In Development — [Try the interactive preview on hedya.dev](https://hedya.dev#tadween)

---

## Why Tadween

Most speech-intelligence pipelines are built as a single monolithic script: load the model, transcribe, diarize, summarize, done. That works for a demo. It falls over in production — memory spikes under load, one slow stage blocks the rest, and swapping a transcription vendor means rewriting the pipeline.

Tadween exists to fix that. It treats each stage of the ASR pipeline as an independently deployable, vendor-agnostic unit, orchestrated through a DAG engine designed for exactly the failure modes that sink naive pipelines: OOM under heavy inference load, unbounded queues, and tight coupling to a single model provider.

```
Media Input
       │
       ▼
[ Preprocess ]     ─── Media validation, FFmpeg transcoding, and drift detection
       │
       ▼
[ Transcription ]  ─── Vendor-agnostic inference port (Whisper, Meta, APIs)
       │
       ▼
[ Diarization ]    ─── Speaker separation and voiceprint matching port
       │
       ▼
[ Summarization ]  ─── Context structuring, topic synthesis, and LLM extraction
```

Every arrow above is a swappable boundary — not a hardcoded call.

---

## Pipeline Stages

| Stage | Status | Package |
|---|---|---|
| Preprocess (media inspection) | **Available** | [`tadween-preprocess`](https://github.com/AHedya/tadween-preprocess) |
| Transcription | Available (bundled) | [`tadween-whisperx`](https://github.com/AHedya/tadween-whisperx) |
| Diarization | Available (bundled) | [`tadween-whisperx`](https://github.com/AHedya/tadween-whisperx) |
| Summarization | Planned | — |

`tadween-whisperx` currently bundles transcription and diarization into a single production-ready port (Whisper + pyannote). Dedicated, independently swappable ports for each stage are on the roadmap.

---

## Core Components

### `tadween-core` — Orchestration Engine
A type-safe DAG orchestration engine purpose-built for stateful, resource-heavy pipelines:
- Managed thread/process task queues, scheduled with hardware-aware backpressure
- Deterministic memory reclamation to prevent OOM during sustained VRAM/RAM-heavy inference
- Designed so each pipeline stage can run as its own microservice under real load (Inspired [resource-semaphore](https://pypi.org/project/resource-semaphore)).

### `tadween-preprocess` — Media Normalization
A resilient, high-concurrency microservice (and client package) for media inspection, FFmpeg-based transcoding, and storage lifecycle orchestration — the first stage any audio needs to pass through before inference. Completely optional but recommended in real-systems to insure integrity and portability (move data in lighter format)

### `tadween-whisperx` — Transcription + Diarization
A production wrapper around WhisperX-style transcription and pyannote-based speaker diarization, with phoneme-level normalization tuned for Egyptian and Levantine Arabic dialects, punctuation restoration, and topic synthesis.

---

## Design Principles

- **Vendor-agnostic ports** — no stage is hardcoded to a specific model provider; swapping Whisper for another transcription engine shouldn't mean rewriting the pipeline.
- **Hardware-aware backpressure** — queues are scheduled with awareness of actual GPU/CPU/memory pressure, not fixed concurrency limits.
- **Memory-safe under load** — deterministic reclamation prevents the OOM crashes that sink naive long-running inference pipelines.
- **Production-grade** — instrumented, resilient and able to work in various environments.
---

## Try It

The full ecosystem isn't hosted as a public service yet, but you can see a live action via the interactive proof-of-concept on hedya.dev:

**[→ Launch the Tadween interactive preview](https://hedya.dev)**

This is a simulation of the pipeline architecture and speaker separation for demonstration purposes — full production hosting is on the roadmap below.

---


## Roadmap

- [ ] Dedicated, independently swappable transcription port (meta/omnilingual, others)
- [ ] Dedicated diarization port
- [ ] Vendor-agnostic summarization port
- [ ] Hosted Tadween service on the [hedya.dev Platform APIs](https://hedya.dev)

---

## Author

Built by **Abdulrahman Hedya** — AI Software Engineer.

[hedya.dev](https://hedya.dev) · [LinkedIn](https://linkedin.com/in/ahedya) · [GitHub](https://github.com/AHedya) · abdulrahman@hedya.dev
