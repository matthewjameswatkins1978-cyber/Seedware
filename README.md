# Seedware

**What if useful software structures could emerge from pressure instead of being designed in advance?**

Seedware is an experiment in computational development. Start with a very small, constrained program, expose it to a changing environment, and observe whether it acquires a useful behaviour that was not explicitly supplied as an abstraction.

Not an AI agent. Not a code-generating chatbot. More of a laboratory for asking where software structure comes from.

## Experiment 001: Rate Mismatch

Imagine a tiny computational organism receiving work in bursts. Sometimes input arrives faster than it can be processed.

Can it discover a way to **decouple receiving from processing and defer work**, even though its initial vocabulary contains no queue, buffer, scheduler, function, or concurrency abstraction?

That is the first research question. A deterministic stack-based virtual machine supplies the environment; a separate development engine will mutate, evaluate, and select candidate programs. The experiment is designed around reproducible outcomes and a withheld maturity test, rather than accepting something merely because its behaviour looks promising.

## Status

**Research design only.** This repository currently contains the experiment build packet, not an executable implementation. No results have been demonstrated, and the central hypothesis remains untested here.

Read the [Experiment 001 build packet](docs/EXPERIMENT_001_BUILD_PACKET.md) for the proposed VM, terminology, methodology, constraints, and acceptance criteria.

## What would count as progress?

1. Implement the small deterministic VM and verify its semantics.
2. Establish a baseline that cannot solve the target rate-mismatch problem.
3. Evolve candidates while retaining lineage and repeatable evidence.
4. Test the selected candidate against the withheld assay.
5. Compare results with controls before claiming anything emerged.

The point is not to produce impressive-looking software. It is to build an artificial universe small enough that an interesting result can be examined rather than explained away.

*No runtime or installation instructions yet. No licence file is currently provided.*
