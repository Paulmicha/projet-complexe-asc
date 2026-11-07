# Host needs inbox compared with DeerFlow, LLM Space, and Meta^n

- **Date:** 2026-11-07
- **Status:** idea / comparison note (not a spec, not an implementation plan)
- **Scope:** how much of the proposed host-needs and skill-feedback plan already exists in DeerFlow, LLM Space, and Meta^n
- **Related:**
  - ASC mother (proposed, not enabled): [Host-wide ASC needs and skill feedback](../../../../../asc/changelog/2026/11/06-host-needs-and-skill-feedback.md)
  - [DeerFlow](https://github.com/bytedance/deer-flow)
  - [LLM Space](https://github.com/deer-flow/llm-space)
  - [Meta^n](https://github.com/minnesotanlp/meta-n)
  - [DeerFlow skill evolution host API](https://github.com/bytedance/deer-flow/blob/main/docs/skill-evolution-host.md)

## Goal of this note

Record the 2026-11-07 comparison of [06-host-needs-and-skill-feedback.md](../../../../../asc/changelog/2026/11/06-host-needs-and-skill-feedback.md) with those three projects. That changelog is a proposal under review. It does not enable capture and it does not change a skill. Paths and review states below describe that proposal.

---

The overlap is small on the mechanism and moderate only on the problem DeerFlow already named: use should produce evidence that can later change a skill, and that change should be checked before it becomes the live skill.

The changelog is a host-local needs inbox for ASC. Ordinary work writes one dated Markdown file per unmet or poorly served need under `$HOME/.local/state/asc/needs/`. A later, separate review decides whether anything should change, and where. None of the three projects is that inbox.

## What lines up

| Changelog piece | DeerFlow 2.0 | LLM Space | Meta^n |
|---|---|---|---|
| Skills as procedures with triggers, resources, and checks | Strong. Skills are Markdown packages, loaded only when the task matches. | Partial. Skills are listed into a thread; the product is a prompt and harness workbench. | None. Improvement is injected Python, not a skill file. |
| Evidence from real runs, then a checked change | Strong, and the closest match. The [skill evolution host API](https://github.com/bytedance/deer-flow/blob/main/docs/skill-evolution-host.md) stores completed-run evidence, stages an immutable skill candidate, runs fail-closed checks, then publishes with version conflict detection and crash recovery. | Partial. Threads keep run history so a person can replay a failure and score it. | Strong, and automatic. Traces feed a fixed meta-operation that writes code for the layer below. |
| Review before shared behavior changes | Partial. The host publishes only after `check` returns `allow`, and only for an existing custom skill’s body and description. The learning policy lives in an optional plugin. The host doc says this slice does not ship a learning plugin. | Partial. Rubrics and verdicts are human and local to one thread. | Weak. Search keeps a monotone archive of candidates and injects helpers that raise a benchmark score, including rollbacks after a regression. |
| Local files, no public sync of the notes | Partial. User skills and DeerMem stay on the machine, but evidence and mutations sit in SQLite or Postgres. | Strong. Threads, traces, and rubrics live under `~/.llm-space`. | Weak. Each run writes `experiments/` with traces, injected code, scores, and LLM logs. |
| Replay to see whether a change holds | Partial. Run events and LLM Space are how DeerFlow itself is debugged. | Strong. That is the product: inspect every harness step, replay, compare two runs. | Strong, inside a benchmark loop, with scores. |

## Where they diverge

**DeerFlow** is a full agent harness: sub-agents, sandboxes, memory, channels, and a database. Its learning surface is an operator-granted plugin API. P0 can update the body and description of an existing user-custom `SKILL.md`. It does not create skills, and it does not record a need before a solution exists. DeerMem is a different loop: it extracts durable user facts and preferences into local Markdown and injects them later. The changelog keeps that kind of memory out of the first slice and keeps the editable procedure in the skill source.

**LLM Space** is the desktop used to build DeerFlow. A thread is an experiment file: system prompt, tools, messages, run history, and a 1–5 rubric. That matches the changelog’s “evaluate behavior, replay near misses” only as a builder activity. It has no cross-project needs directory, no review states (`new`, `reviewed`, `planned`, `addressed`, `deferred`, `duplicate`), and no rule for which repository a fix belongs in.

**Meta^n** is a research stack. One fixed operation reads traces and writes executable `pre_process` hooks plus a `code_library`, then the stack is searched against benchmarks. The shared sentence is “traces should change later behavior.” The control model is the opposite of the changelog: depth emerges by injecting code, and success is a score. The changelog’s loop is capture, human review, a small edit to an owned source, then a representative replay.

## What the changelog has that these three do not

- A **need** as the desired outcome, recorded before anyone picks an action, a skill edit, or “do nothing.”
- One host directory shared by every ASC instance for the same user, with private detail left in the owning project.
- Placement on ASC’s genericity scale: core, contrib, instance `extend/`, or an existing skill, after review.
- Capture that must not block the task in progress, and that stays quiet on routine success.
- First slice with no database, queue, writer, or new entity. A writer is conditional on later friction.

So: DeerFlow already built the guarded “evidence, then publish a skill edit” half, as infrastructure for a plugin. LLM Space already built the inspect-and-replay bench for that harness. Meta^n already built automatic trace-to-code improvement for benchmarks. The changelog’s distinct piece is the pre-solution record and the human placement decision, stored as plain dated files, aimed at ASC’s shell vocabulary rather than at an agent runtime.
