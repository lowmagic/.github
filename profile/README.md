<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/lowmagic-lockup-dark.svg">
    <img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/lowmagic-lockup-light.svg" alt="lowmagic" width="200">
  </picture>
</p>

Software systems and local-first AI tools by [Nathan Curry](https://nathancurry.com) ([LinkedIn](https://www.linkedin.com/in/nathandcurry)), a software maintenance engineer in Red Hat's OpenShift Enhanced Solution Support team.

Everything here is built [jungle style](https://github.com/lowmagic/.github/blob/main/PRINCIPLES.md): use what's already racked. The [standards](https://github.com/lowmagic/.github/blob/main/code-standards/README.md) turn that into rules you can check in review.

## Conversation-to-idea pipeline

Turns recorded conversations into a searchable map of the ideas in them, scheduled across a home NAS and a GPU host without cron delays:

```mermaid
flowchart LR
  A["Pendant & Mac audio"] --> H["hearsay: transcribe, diarize, attribute"]
  H -- "JSONL utterance stream" --> IM["ideamachine: episodes, cited extraction, web/MCP"]
  C["corral: priority queue (GPU & NAS workers)"] -. "leases GPU transcription" .-> H
  C -. "triggers run on new turns" .-> IM
```

| Project | What it is | Worth a look |
| --- | --- | --- |
| <picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/ideamachine-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/ideamachine-mark-light.svg" alt="Idea Machine" width="28" align="absmiddle"></picture> [ideamachine](https://github.com/lowmagic/ideamachine) | Segments transcripts into content-addressed episodes, extracts ideas, tasks, and decisions via Claude with citations, and serves the map over restricted Postgres views (`pub`). | Cheap triage models were [measured against hand labels](https://github.com/lowmagic/ideamachine/blob/main/docs/decisions/0002-first-triage-eval.md) before being trusted, then [retired](https://github.com/lowmagic/ideamachine/blob/main/docs/decisions/0014-retire-routing.md) when replay data showed routing wasn't earning its keep |
| <picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/hearsay-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/hearsay-mark-light.svg" alt="Hearsay" width="28" align="absmiddle"></picture> [hearsay](https://github.com/lowmagic/hearsay) | Pendant (iOS) and Mac capture, self-hosted Parakeet transcription and pyannote diarization, and owner voice attribution. Audio never leaves the tailnet. | Precision-first speaker attribution, deterministic rebuilds from raw uploads, and a versioned [stream contract](https://github.com/lowmagic/hearsay/blob/main/docs/stream.md) with deletion signals |
| <picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/corral-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/corral-mark-light.svg" alt="Corral" width="28" align="absmiddle"></picture> [corral](https://github.com/lowmagic/corral) | Priority job scheduler for opportunistic local compute across `gpu` and `nas` workers. | Priority aging, burst coalescing (`wait_seconds`), persistent worker pause, and automatic yield when another process uses the GPU |

## Support-case triage

<picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/compa-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/compa-mark-light.svg" alt="compa" width="28" align="absmiddle"></picture> **[compa](https://github.com/lowmagic/compa)**: a terminal chat companion in Go for investigating support cases and diagnostic archives. It reads case files through deterministic tools in a sandbox, redacts secrets before anything leaves the host, makes no network calls by default, and checks every answer against the evidence it cites. It does not diagnose: the engineer draws the conclusions.

- **[compa-engine](https://github.com/lowmagic/compa-engine)**: the Go library behind `compa`, owning sandboxed collection, cache-friendly conversation order, redaction, digest-pinned bundles, and evidence checks ([spec](https://github.com/lowmagic/compa-engine/blob/main/docs/SPEC.md), [architecture](https://github.com/lowmagic/compa-engine/blob/main/docs/ARCHITECTURE.md)).
- **[compa-experts](https://github.com/lowmagic/compa-experts)**: embedded domain experts, shared file-inspection tool catalogs, and case routing rules.
- **[prometheus-tsdb-reader](https://github.com/lowmagic/prometheus-tsdb-reader)**: static Go CLI and library that answers PromQL offline from a captured Prometheus TSDB archive, salvaging readable blocks and partial WAL records from damaged dumps and bounding output for a sandboxed caller ([spec](https://github.com/lowmagic/prometheus-tsdb-reader/blob/main/docs/SPEC.md), [decisions](https://github.com/lowmagic/prometheus-tsdb-reader/blob/main/docs/stack.md)).

## Smaller tools

| Project | What it is | Worth a look |
| --- | --- | --- |
| <picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/hypertrace-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/hypertrace-mark-light.svg" alt="Hypertrace" width="28" align="absmiddle"></picture> [hypertrace](https://github.com/lowmagic/hypertrace) | Bounded web-research harness where an LLM proposes searches, but code decides what counts as evidence. | Verbatim byte-offset quote checks in article bodies, adversarial review, per-attempt cost accounting, and code-written `"Inconclusive"` conclusions ([sample report](https://github.com/lowmagic/hypertrace/blob/main/docs/sample-report.md)) |
| <picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/backtalk-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/backtalk-mark-light.svg" alt="backtalk" width="28" align="absmiddle"></picture> [backtalk](https://github.com/lowmagic/backtalk) | Local GPU speech server for real-time voice chat at home over Tailscale, speaking the OpenAI Realtime protocol (Silero VAD + Parakeet ONNX). | Read-back-only server that refuses ungrounded replies, cancels on barge-in, shares the GPU with `corral`, and speaks through `chimera-tts` |
| <picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/chimera-tts-mark-dark.svg"><img src="https://raw.githubusercontent.com/lowmagic/.github/main/profile/assets/chimera-tts-mark-light.svg" alt="chimera-tts" width="28" align="absmiddle"></picture> [chimera-tts](https://github.com/lowmagic/chimera-tts) | Text-to-speech voice and browser chat built on a physical vocal-tract synth rather than neural TTS ([live demo](https://lowmagic.github.io/chimera-tts/)). | Pitch and rhythm learned from recordings over an unpatched synth, Markdown-to-speech normalizer, and no GPU or API key |

Go, Python, Postgres + pgvector, SQLite, Claude, local models, TypeScript, Swift, Docker, Ansible, Tailscale.
