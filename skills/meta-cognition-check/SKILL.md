---
name: meta-cognition-check
description: >
  Execute this skill immediately when a task, issue, or plan has been explicitly
  confirmed by the user — before writing any code, making any edits, or producing
  any output. This skill runs a structured self-assessment to determine whether the
  current model (typically Flash-class) can solve the task, or whether escalation
  to a higher-tier model (e.g., Pro) is required. Trigger on any explicit task
  confirmation phrases such as "let's do it", "go ahead", "start on this",
  "implement this", or when a concrete deliverable has just been agreed upon.
  Do NOT skip this skill to save time — a false start costs more than the check.
---

# Meta-Cognition Check (MCC)

A pre-execution self-assessment workflow.  
Run this **before** any task execution. Its purpose is to surface "cannot solve"
declarations **before** failure, not after — replacing apologies with structured
impossibility reports.

---

## When to Run

| Situation | Run MCC? |
|---|---|
| User explicitly confirms a task or issue | ✅ Always |
| New sub-task delegated mid-session | ✅ Always |
| Entering a plan execution phase | ✅ Always |
| Clarifying a question or chatting | ❌ Skip |
| Already inside an MCC-approved task | ❌ Skip |

---

## Phase 0 — Internal Self-Assessment (Do NOT output this phase)

Work through the following checks silently.

### Step 1: Task Structure Scan

- **Task category** — Classify as one of:
  `reasoning` / `code-generation` / `research` / `parallel-planning` /
  `creative` / `long-context-tracking` / `mixed`

- **Sub-task count (estimate)** — How many distinct steps does this require?

- **Sub-task independence** — Can steps run independently?
  `high` (mostly independent) / `medium` / `low` (tightly sequential)

- **Prerequisite information completeness**
  `sufficient` / `partial` / `insufficient`

### Step 2: Self-Capability Matching

Answer each honestly.

| Check | Assessment |
|---|---|
| Does this require parallel reasoning across ≥3 independent branches? | yes / no |
| If yes — can this model handle that structurally? | capable / not-capable |
| Estimated token footprint | small (<2K) / medium (<8K) / large (<32K) / xlarge (32K+) |
| Does xlarge footprint risk context exhaustion mid-task? | yes / no |
| Does this require domain expertise, real-time data, or high numeric precision? | yes / no |
| If yes — is hallucination risk high? | yes / no |
| Does this require tracking a deep, long dependency chain? | yes / no |

### Step 3: Success Rate Scoring

Combine Step 1 and Step 2 into an estimated success rate.

Use the following as anchors — adjust based on task specifics:

| Condition | Impact |
|---|---|
| Parallel reasoning required + not-capable | −40% |
| Prerequisite information insufficient | −30% |
| Hallucination risk high | −25% |
| Token footprint xlarge + context risk | −20% |
| Deep dependency chain tracking required | −15% |
| Task category is well within capability | +20% |
| All prerequisites available and clear | +15% |

Start from 70% baseline and apply adjustments.  
Cap at 95% (never claim certainty). Floor at 5%.

---

## Phase 1 — Output

Output **exactly one** of the following blocks. No preamble. No explanation outside the block.

---

### If success rate ≥ 80%: GO

```
[MCC: GO]
Estimated success rate: XX%
Proceeding with the task.
```

---

### If success rate 50–79%: CONDITIONAL

```
[MCC: CONDITIONAL]
Estimated success rate: XX%

Conditions before proceeding:
- {Specific prerequisite that must be clarified}
- {Sub-task that should be split or scoped down}

Awaiting confirmation before starting.
```

---

### If success rate < 50%: NO-GO

```
[MCC: NO-GO]
Estimated success rate: XX%

This task cannot be resolved by the current model.

Reasons:
- [ ] Parallel reasoning required — structurally not supported at this tier
- [ ] Token footprint exceeds safe context limits for this task
- [ ] High hallucination risk — specialized knowledge or real-time data required
- [ ] Prerequisite information is insufficient — reasoning would diverge
- [ ] Deep dependency chain exceeds reliable tracking capacity

Recommended action:
→ Switch to a higher-tier model (e.g., Pro / Opus)
→ Split the task — suggested breakdown: {brief split proposal}
→ Human input required to supply missing prerequisites

This report replaces execution. No further output will be generated for this task.
```

---

## Behavior Rules

1. **Never skip MCC** when a task has been explicitly confirmed.
2. **Never apologize** in a NO-GO. Report impossibility as a structural fact.
3. **Never attempt the task after a NO-GO** — stop cleanly and wait.
4. **CONDITIONAL** requires a human response before re-evaluation. Do not self-resolve.
5. Phase 0 is internal — output only Phase 1.
6. The success rate is an honest estimate, not a hedge. Do not inflate it.

---

## Design Intent

This skill exists because the cost of a confident failure exceeds the cost of an
honest NO-GO. Flash-class models are fast and cheap — but their limits are real.
Detecting those limits before execution allows the human to make a routing
decision: escalate to Pro, restructure the task, or supply missing context.

The goal is not to be conservative. It is to be accurate.
