# Notes for readers

Questions that come up about this work, answered directly.

## Is there an implementation in this repository?

No. This repository documents the paper: the method, the results, the references, and the limits. The evaluation was run for the paper rather than packaged as a release, and shipping code that has not been run against the paper's dataset would invite a reproduction question it could not answer.

What is here is enough to implement the method from, since the equations in [`method.md`](method.md) are the ones the paper specifies.

## What dataset was used?

The paper describes a large corpus of source code annotated with bugs and their corresponding fixes, together with project architecture, dependency, and runtime metadata. It does not name a public benchmark.

This matters for anyone planning to build on the work. The dataset requirement is also the paper's first stated limitation, and it is the reason the method is harder to apply to a new language than the accuracy figures alone suggest.

## Why two models instead of one?

Because they see different things and the paper measures the difference.

A transformer over tokens and syntax reads what the code says. A graph network over module dependencies reads who the code talks to. A bug that exists only because two modules disagree leaves no evidence inside either module's token stream, so no amount of scaling the transformer finds it.

The combined model scores 91.4% against 88.2% and 85.7%. That 3.2-point gain over the better branch is the entire argument for carrying a second architecture.

## Why does the graph model score lowest on its own?

It should. Module structure tells you nothing about whether a function is correct. Given only the dependency graph, the model is guessing from shape: how connected a module is, how central, how deep in the tree. That is a weak signal alone and a useful one in combination.

A reader should be suspicious if it had scored close to the transformer, since that would suggest the transformer was underperforming rather than the graph branch excelling.

## Does the fix get applied automatically?

A patch is generated and then validated by unit tests and runtime analysis. The pipeline applies validated fixes. At an 88% success rate, roughly one generated patch in eight is wrong, which is exactly why the validation step is not optional.

The design position is that the system proposes and the tests decide.

## How does this differ from a language model that writes patches?

Two things.

The input includes runtime and architectural metadata, not just code. A general code model sees the file; this sees the file plus the environment it runs in.

The training objective is detection first, generation second. Sequence-to-sequence repair, where a model is trained to emit the fixed version of buggy code, has a known failure mode the paper describes: buggy and fixed code overlap heavily, so the model learns to copy. Copying scores well on similarity metrics and changes nothing.

## Can I use this on my codebase today?

Realistically, not without work. You would need bug-annotated history for your code, runtime instrumentation good enough to produce the metadata, and a validation suite strong enough to catch the 12% of patches that are wrong.

The paper's contribution is the finding that context improves detection and that the combination is workable inside CI/CD. It is not a shipped tool.

## Where should someone take this next?

The paper names the open problems. Generalisation across languages and frameworks is untested. Performance under limited annotation is unknown. Runtime overhead on very large dependency graphs may offset the gains.

The dataset question is the one that gates the others. A public, multi-language corpus with bugs, fixes, and runtime metadata would let the rest be answered.
