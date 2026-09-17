# SEEDWARE
## Experiment 001: Rate Mismatch

You are building the first working experiment for a research project called **Seedware**.

Seedware investigates whether useful software structures can emerge from computational pressure rather than being explicitly designed in advance.

This is **not** an autonomous AI agent project.

Do not add LLMs, agent frameworks, databases, networking stacks, MCP, web interfaces, dashboards, orchestration frameworks, or speculative future architecture.

The first experiment should be small enough that we can understand every moving part.

The core research question is:

> **Can a tiny computational organism develop a decouple-and-defer mechanism when exposed to transient rate mismatch, without any queue, buffer, function, scheduler, or concurrency abstraction existing in its starting vocabulary?**

We are not trying to make impressive software.

We are trying to make a clean artificial universe.

---

# 1. Repository

Create a new Rust repository:

```text
seedware/
```

Suggested structure:

```text
seedware/
├── Cargo.toml
├── README.md
├── docs/
│   ├── EXPERIMENT_001.md
│   ├── VM_SPEC.md
│   ├── METHODOLOGY.md
│   └── TERMINOLOGY.md
├── src/
│   ├── main.rs
│   ├── vm.rs
│   ├── program.rs
│   ├── environment.rs
│   ├── evaluator.rs
│   ├── mutation.rs
│   ├── evolution.rs
│   ├── lineage.rs
│   └── report.rs
├── tests/
│   ├── vm.rs
│   ├── environment.rs
│   ├── determinism.rs
│   └── experiment.rs
└── runs/
    └── .gitkeep
```

Keep dependencies minimal.

Reasonable dependencies:

```text
clap
serde
serde_json
rand
rand_chacha
sha2
```

Do not introduce a framework unless it solves a demonstrated implementation problem.

Everything must be cross-platform by default.

Do not hard-code PowerShell, Windows paths, drive letters, `/bin/bash`, Unix signals as the only control mechanism, or other platform-specific assumptions.

Platform-specific behaviour must sit behind explicit boundaries.

---

# 2. Terminology

Use these terms consistently.

## Constitution

Rules the evolving organism cannot modify.

Examples:

- VM semantics
- memory limit
- evaluator
- mutation permissions
- lineage recording
- maximum execution budget
- hidden maturity assay

## Niche

The environment applying pressure to the organism.

Examples:

- input timing
- burst pattern
- resource scarcity
- required observable behaviour

## Seed

The initial tiny working program.

## Development Engine

The external machinery that:

- mutates candidates
- evaluates candidates
- selects survivors
- creates branches
- records ancestry

The Development Engine is **not part of the phenotype**.

## Phenotype

The actual program executing inside the VM.

## Ontogeny

The developmental history of one lineage.

## Generation

One accepted phenotype descended from another accepted phenotype.

## Maturity Assay

A withheld final evaluation testing whether the organism developed the required adult behaviour.

---

# 3. The Virtual Machine

Build a deterministic integer stack VM.

The VM itself is part of the immutable Constitution.

Do not modify the instruction set during Experiment 001.

Use **nine primitive operations**.

```text
DUP
DROP
SWAP
ADD
LOAD
STORE
JZ
READ
EMIT
```

Additionally allow signed integer literals directly in the instruction stream.

A literal is data, not a primitive operation.

Example:

```text
3
4
ADD
```

leaves `7` on the stack.

## Required semantics

### DUP

Duplicate top stack item.

### DROP

Remove top stack item.

### SWAP

Swap top two stack items.

### ADD

Pop two integers and push their sum.

Negative literals therefore permit subtraction.

### LOAD

Pop address.

Push value from linear memory.

### STORE

Pop address and value and store the value at that memory location.

Document the exact operand order and keep it fixed.

### JZ

Instruction contains or consumes a relative jump offset.

Pop condition.

If condition == 0, alter program counter by the specified offset.

Otherwise continue.

Choose one clean representation and document it formally.

A deliberate unconditional jump can be constructed using a zero literal.

### READ

Attempt to consume the current ingress item.

If one is available, push its integer value.

If no value is currently available, push a reserved sentinel.

Use:

```text
-1 = no input
```

Therefore Experiment 001 environmental input values must be restricted to:

```text
0..255
```

### EMIT

Pop one integer and submit it to the external output stream.

---

# 4. VM Limits

Make these configurable but give Experiment 001 deterministic defaults.

Suggested defaults:

```text
stack capacity:       64 integers
linear memory:        64 integers
program size:         256 instruction/data tokens
maximum cycles/run:   configurable
integer representation: i32
```

Illegal memory access, stack underflow, overflow, invalid jump or execution beyond the cycle budget must terminate the candidate with a structured failure result.

Do not permit undefined behaviour.

---

# 5. Environment

Experiment 001 processes an integer stream.

For every input `x`, the organism must eventually emit:

```text
f(x) = (3 * x) + 1
```

The primitive machine has no multiplication operation.

The organism must construct this from primitive operations.

Outputs must preserve input ordering.

The environment controls when inputs become available.

Critically, the environment does NOT explain failures.

It may report:

```text
expected_output_count
observed_output_count
incorrect_output
timeout
memory_violation
illegal_instruction
```

It must NOT report:

```text
you need a queue
buffer overflow
store input before processing
use a ring buffer
```

Core principle:

> **The environment may reveal failure. It must not reveal architecture.**

---

# 6. Ingress Semantics

Avoid accidentally placing a queue in the environment.

There is exactly **one ingress slot**.

When the environment presents an input value, it remains available for a fixed dwell period.

If the phenotype executes `READ` during that period, the value is consumed.

If it does not, the value expires and is counted as lost.

There is no hidden backlog.

The environment must never preserve arbitrary unread inputs.

This is extremely important.

---

# 7. Seed 0

Seed 0 must already solve the easy version of the task.

It should:

```text
READ
process x into 3x+1
EMIT
repeat
```

It may contain enough branching to distinguish `-1` from valid input.

Keep Seed 0 as small and obvious as practical.

Seed 0 should pass the baseline environment.

Seed 0 should reliably fail the burst environment.

Document its exact bytecode/program representation.

---

# 8. Development Curriculum

Experiment 001 has developmental stages.

Do not expose the organism immediately to the final adult environment.

## Stage A: Baseline

Inputs arrive slowly.

There is enough time to:

```text
READ
compute
EMIT
```

before the next input arrives.

Seed 0 must pass.

## Stage B: Mild Rate Pressure

Occasional pairs or small groups arrive closer together.

The direct implementation begins to experience pressure.

## Stage C: Burst Environment

Normal average input rate remains below processing capacity.

But short bursts exceed processing capacity.

Example conceptually:

```text
normal:
one value every 30 VM cycles

burst:
one value every 3 VM cycles
for 8-16 values

recovery:
long quiet interval
```

Exact timings should be calibrated experimentally so that:

1. Seed 0 passes baseline.
2. Seed 0 loses data under bursts.
3. Capturing inputs into bounded internal memory and processing them later is sufficient.
4. The total workload is physically solvable.
5. No infinite backlog is required.

The goal is transient overload, not permanently impossible throughput.

---

# 9. Do Not Specify the Solution

The evaluator must accept any solution satisfying observable behaviour and resource limits.

Potential successful strategies may include:

```text
ring-style storage
linear staging
backpressure-like behaviour if possible
batching
multiple processing phases
a strange state machine
something we did not anticipate
```

Never award points for looking like a queue.

Never search source code for queue patterns.

Never reward variable names or structural resemblance to human implementations.

---

# 10. Evolution / Search

Use a simple reproducible evolutionary search.

Suggested starting algorithm:

```text
population size: 128
elite survivors: 8
offspring per elite: 16
```

These are defaults, not sacred numbers.

Every run must accept a deterministic RNG seed.

Use `ChaCha8Rng` or another deterministic seeded RNG.

Mutation operators may include:

```text
insert instruction
delete instruction
replace instruction
change literal
change jump offset
duplicate short instruction sequence
move short instruction sequence
```

Keep mutation local.

Do not give the mutator knowledge of queues, loops, functions, buffers, state machines, or the target architecture.

It manipulates syntax only.

---

# 11. Fitness

Use lexicographic priorities rather than one opaque weighted score.

Suggested ordering:

## Tier 1: Constitutional validity

Candidate must not:

- crash the VM
- access illegal memory
- violate resource boundaries
- exceed hard execution ceiling

## Tier 2: Correctness

Maximise:

```text
correct outputs
correct ordering
absence of duplicates
absence of missing outputs
```

## Tier 3: Survival under environmental pressure

Measure successful capture and completion under bursts.

## Tier 4: Efficiency

Only after correctness:

```text
fewer VM cycles
less memory touched
smaller program
```

This prevents a fast wrong program defeating a slower correct one.

Keep the exact fitness components visible in recorded results.

---

# 12. Lineage / Fossil Record

Every accepted generation must leave an immutable structured record.

Use JSONL.

Example:

```json
{
  "generation": 42,
  "parent": 37,
  "rng_seed": 918273,
  "program_hash": "...",
  "program_size": 91,
  "mutation": {
    "kind": "duplicate_sequence",
    "details": "..."
  },
  "environment": {
    "stage": "burst",
    "scenario_hash": "..."
  },
  "fitness": {
    "valid": true,
    "correct": 16,
    "missing": 2,
    "duplicates": 0,
    "cycles": 1821
  },
  "accepted": true
}
```

Record rejected candidates as well, preferably separately or with `accepted:false`.

For each experiment run preserve:

```text
config.json
lineage.jsonl
best_program.txt
best_program.json
summary.json
```

The experiment must be completely replayable from:

```text
source revision
experiment config
random seed
```

---

# 13. No Runtime MDL / Function Invention Yet

Do NOT add callable labels, learned opcodes, macros or runtime functions in Experiment 001.

That would risk giving the organism the abstraction we later claim it discovered.

If you implement compression for storage/reporting purposes, it must be semantics-neutral and expand completely back into the original nine-opcode VM before execution.

The executable universe remains fixed.

Later experiments can explicitly study abstraction/library learning.

Not this one.

---

# 14. Control Groups

Implement at least three modes.

## Control A: Frozen Seed

Run Seed 0 through all environments without evolution.

Expected:

```text
baseline passes
burst environment fails
```

## Control B: Random Mutation Without Selection

Apply the same number of mutations as the evolutionary experiment but do not select for environmental performance.

Expected:

no systematic adaptation.

## Experimental Group

Mutation + environmental selection.

This is essential.

Without controls, a successful evolved candidate proves very little.

---

# 15. Repeated Runs

Do not judge the experiment from one lucky run.

CLI should support:

```text
seedware run --seed 1
seedware run --seed 2
seedware run --seed 3
```

and:

```text
seedware batch --from 1 --to 100
```

Each run must be independent and deterministic.

For the first milestone, 20 repeated runs are enough to validate infrastructure.

The architecture should support hundreds or thousands later.

---

# 16. What Counts as an Interesting Result?

Experiment 001 is successful as infrastructure if:

```text
Seed 0 reliably passes baseline.
Seed 0 reliably fails burst conditions.
Control runs do not systematically solve the burst niche.
Evolutionary runs sometimes produce phenotypes that do.
Results are deterministic when rerun with the same seed.
Lineages are reproducible and inspectable.
No queue/buffer primitive exists in the VM.
No evaluator knowledge leaks into the mutator.
```

A scientifically interesting result would be stronger:

Across independent runs, successful phenotypes repeatedly develop some internal mechanism that:

```text
captures inputs rapidly
retains multiple pending values
processes them later
preserves required output behaviour
```

We should NOT initially call this a queue.

Call it:

> **decouple-and-defer behaviour**

Only after the experiment should we analyse what structure actually emerged.

---

# 17. Ablation Support

Design the experiment so that successful programs can later undergo lesion/ablation analysis.

We should be able to:

```text
disable instruction range
zero selected memory cells
replace selected instructions with NOP-equivalent behaviour
rerun maturity assay
```

Do not necessarily automate sophisticated causal analysis yet.

But make the VM and reporting architecture capable of supporting it.

---

# 18. Hidden Maturity Assay

Create a final set of burst patterns that are NOT used directly during candidate evolution.

Use them only to evaluate the final phenotype.

This helps distinguish:

```text
adaptation to the actual computational pressure
```

from:

```text
overfitting to the exact training schedule
```

Record both:

```text
development fitness
maturity assay fitness
```

---

# 19. Tests

Write strong tests before declaring Experiment 001 complete.

At minimum test:

```text
all VM instructions
stack boundaries
memory boundaries
jump semantics
READ dwell expiry
EMIT ordering
cycle limits
deterministic RNG
identical replay from same seed
different RNG seeds produce different searches
Seed 0 baseline success
Seed 0 burst failure
control behaviour
lineage integrity
program hashing
hidden assay separation
```

No test should depend on PowerShell unless the test is explicitly testing Windows/PowerShell behaviour.

Ordinary tests must run on Windows, Linux and macOS.

---

# 20. CLI

Provide something approximately like:

```text
seedware show-seed

seedware evaluate \
  --program seed0 \
  --environment baseline

seedware run \
  --seed 42 \
  --generations 500

seedware batch \
  --from 1 \
  --to 20 \
  --generations 500

seedware replay \
  --run runs/exp001-seed-42

seedware inspect \
  --run runs/exp001-seed-42
```

Exact command names may change if a cleaner CLI emerges.

Keep it simple.

---

# 21. Human-Readable Reporting

At the end of each run print something like:

```text
SEEDWARE EXPERIMENT 001

Run seed: 42

Seed 0:
  baseline: PASS
  burst:    FAIL

Evolution:
  attempted candidates: 51,200
  accepted generations: 73

Best phenotype:
  baseline: PASS
  training burst: PASS
  hidden maturity assay: PASS

Program size:
  Seed 0: 18 tokens
  Adult:  67 tokens

Peak memory touched:
  11 cells

Outputs:
  expected: 128
  correct: 128
  missing: 0
  duplicate: 0

Result:
  candidate developmental adaptation observed
```

Do not automatically claim that a queue evolved.

That conclusion belongs to later analysis.

---

# 22. README

The README must explain Seedware in plain English.

Suggested opening:

> **Seedware studies software that develops rather than being fully designed.**
>
> A tiny computational seed is placed in an environment containing measurable pressures. Candidate changes are generated without knowledge of conventional software architecture and retained only when they improve survival or correctness.
>
> Experiment 001 asks whether transient rate mismatch can cause a primitive program to develop a mechanism for separating input capture from later processing.

Also clearly state:

```text
Seedware is experimental research software.
Experiment 001 does not use AI.
The first VM intentionally contains no queue, buffer, function or scheduler abstraction.
```

---

# 23. Engineering Rules

Optimise for:

```text
determinism
explicit semantics
small components
inspectability
reproducibility
cross-platform behaviour
bounded execution
strong tests
minimal dependencies
```

Avoid:

```text
clever frameworks
premature generalisation
unnecessary async
distributed systems
AI dependencies
dynamic plugin systems
UI work
network services
speculative extensibility
```

This project is intentionally tiny.

Do not turn the experimental apparatus into the thing being experimented on.

---

# 24. Implementation Stages

Work in this order.

## M1 — VM

Build and fully test the deterministic VM.

## M2 — Environment

Implement baseline and burst niches.

Prove Seed 0 passes baseline and fails burst.

STOP here if that property is not cleanly demonstrated.

## M3 — Mutation and Evaluation

Build deterministic mutation and fitness evaluation.

## M4 — Evolution

Run selectable populations and record lineage.

## M5 — Controls

Implement frozen and unselected controls.

## M6 — Hidden Maturity Assay

Add held-out environments.

## M7 — Repeated Runs

Run at least 20 independent deterministic seeds.

## M8 — Evidence

Generate the first experiment report.

Do not proceed to more sophisticated Seedware experiments until Experiment 001 is understandable and reproducible.

---

# 25. Final Deliverable

When finished, report:

```text
1. Final commit SHA.
2. Repository tree.
3. Exact VM specification.
4. Exact Seed 0 program.
5. Exact environmental schedules.
6. Mutation operators.
7. Selection algorithm.
8. Fitness ordering.
9. Test results.
10. Results of at least 20 independent runs.
11. Control-group results.
12. Number of runs that passed the hidden maturity assay.
13. The most interesting evolved phenotype found.
14. Its complete lineage.
15. Whether you see evidence of decouple-and-defer behaviour.
16. Anything in the result that surprised you.
17. Any place where the experiment appears biased or invalid.
```

Be sceptical.

If Experiment 001 does not produce the hoped-for behaviour, that is a valid result.

Do not alter the experiment after seeing results merely to force success without documenting the change.

If calibration must change, version the experiment:

```text
001a
001b
001c
```

Preserve earlier results.

The purpose is not to prove Seedware right.

The purpose is to find out whether there is something here.
