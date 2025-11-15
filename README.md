# Deep Learning for Contextual Bug Detection and Automated Fixes in Software Systems

<img width="1500" height="700" alt="Deep Learning for Contextual Bug Detection and Automated Fixes in Software Systems" src="https://github.com/user-attachments/assets/393f290c-1818-4496-a89d-45e7759f3016" />


Published at the **2024 2nd International Conference on Advances in Computation, Communication and Information Technology (ICAICCIT)**, IEEE, pp. 624–629.

**Paper:** [IEEE Xplore](https://ieeexplore.ieee.org/document/10912101) · **DOI:** [10.1109/ICAICCIT64383.2024.10912101](https://doi.org/10.1109/ICAICCIT64383.2024.10912101)

This repository is the reference companion to the paper: what the work claims, how the method is put together, what the numbers were, and where the limits are. The full text lives on IEEE Xplore and is not redistributed here.

---

## The problem, in one paragraph

Static analysers and code review look at code in pieces. They read a function, a file, a diff. A large share of production bugs are not in any one of those pieces. They live in the interaction: module A calls B, B quietly changed what it returns, and the failure surfaces somewhere neither author was looking. A tool that only ever sees isolated fragments cannot find that class of bug, because the evidence for it is not inside any single fragment.

This work asks whether giving a model the surrounding context, meaning the project architecture, the dependency structure, and observed runtime behaviour, lets it catch defects that per-file analysis misses, and whether the same model can propose the patch.

## What was built

Two branches over the same file, joined by one classifier.

A **transformer** reads the token stream and the syntax tree. That covers what the code says: the syntactic and semantic view a language model is good at.

A **graph neural network** reads a dependency graph whose nodes are modules and whose edges are the relationships between them. That covers who the code talks to.

The two representations are concatenated and scored together. When the detector flags a file, a decoder conditioned on both representations generates a candidate fix, which is then validated by unit tests and runtime analysis rather than trusted on sight. The whole thing sits inside a CI/CD pipeline so detection and patch generation happen on the commit rather than in a review queue days later.

The mechanism that matters is the metadata. Input is not the code alone but `X = [C, M]`, where `M` carries architecture, dependencies, and runtime conditions. That is the difference between a model that can only ask "is this function wrong" and one that can ask "is this function wrong *given what it depends on and how it behaves in production*".

Full method, with the equations as published: [`docs/method.md`](docs/method.md).

## Results

Reported in the paper. These are the paper's measurements, on the paper's evaluation setup.

**Detection quality**

| Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|
| Transformer | 88.2 | 86.9 | 87.4 | 87.1 |
| Graph neural network | 85.7 | 84.2 | 85.1 | 84.6 |
| **Combined** | **91.4** | **90.8** | **91.1** | **91.0** |

The combination beats either branch by roughly three points of accuracy over the transformer and close to six over the graph model. That gap is the paper's central claim: the two views are complementary rather than redundant.

Worth noting which branch is weaker alone. The graph model is the lower scorer on its own, which is what you would expect, since structure without content cannot tell a correct function from a broken one. Its contribution shows up only in combination.

**Time to detect and fix**

| Method | Detection (s) | Fixing (s) | Total (s) |
|---|---|---|---|
| Traditional static analysis | 25.4 | 8.3 | 19.2 |
| Transformer | 12.3 | 6.5 | 18.8 |
| **Combined** | **8.7** | **4.8** | **13.5** |

**Inside a CI/CD pipeline**

| Pipeline | Average build (s) | Bug detection rate | Automated fix success |
|---|---|---|---|
| Without model integration | 45.7 | 65% | 62% |
| **With model integration** | **32.2** | **91%** | **88%** |

The build got faster, not slower, which is the counterintuitive result. Catching a defect during the build costs less than the rebuild it prevents.

A note for anyone reading the tables closely: in the time table, the first row's total does not equal its two components, while the other two rows do. The paper's discussion section compares 13.5 s against 25.4 s for traditional analysis, so 25.4 is the figure the narrative treats as the static-analysis baseline. Flagging it here so nobody has to wonder whether they misread it.

## What the paper concludes

Contextual information improves both the accuracy and the relevance of automated bug detection and repair. Models given architecture and runtime metadata found defects that per-fragment analysis missed, specifically the ones arising from component interaction and dynamic behaviour. Putting those models in a CI/CD pipeline made real-time detection and patch generation workable rather than theoretical, and cut detection and repair time substantially.

The broader argument is about direction of travel: less manual intervention, better code dependability, and a realistic path toward software that repairs a meaningful fraction of its own defects.

## Where it does not work yet

Stated plainly in the paper, and repeated here because a companion repo that only lists strengths is not worth reading.

**It needs a lot of labelled data.** The method depends on a large corpus of code annotated with bugs and their corresponding fixes. Where that corpus is small or absent, performance degrades. This is the binding constraint on applying it to a new language or a private codebase.

**Generalisation is unproven.** Results hold for the languages and settings tested. Whether they transfer across programming languages and frameworks needs further work, and the paper does not claim otherwise.

**Runtime overhead at scale.** In very large projects with deep dependency graphs, collecting and processing the runtime data that makes the method work carries a cost that can offset the gains.

## Where the field was

Twenty-two references, grouped by what they contribute to this work: [`docs/related-work.md`](docs/related-work.md).

The short version. Prior program repair split three ways: edit-based methods that localise and patch directly, neural machine translation methods that treat repair as buggy-to-fixed sequence translation, and more recent LLM-based approaches. The NMT framing is easy to implement but has a known failure mode, since buggy and fixed code overlap heavily, models learn to copy rather than to edit. Edit-based methods avoid that but need fiddly multi-step machinery and often assume the broken code parses into a graph, which is exactly what a syntax error breaks. This work sits alongside the context-aware line of research, taking the position that the missing signal is environmental rather than architectural.

## Authors

**Kushal Gaddamwar**, PDPM IIIT Jabalpur, Computer Science and Engineering, Madhya Pradesh, India

Yatharth Srivastava, The LNM Institute of Information Technology, Jaipur
Jyoti Parashar, ADGIPS, New Delhi
Ritwij Aryan Parmar, Manipal Institute of Technology, Karnataka
Aditya Kumar Pandey, Manipal Institute of Technology, Karnataka
Virendra Singh Kushwah, VIT Bhopal University, School of Computing Science and AI, Sehore

## Citing this

```bibtex
@inproceedings{gaddamwar2024contextual,
  title     = {Deep Learning for Contextual Bug Detection and Automated Fixes in Software Systems},
  author    = {Gaddamwar, Kushal and Srivastava, Yatharth and Parashar, Jyoti and
               Parmar, Ritwij Aryan and Pandey, Aditya Kumar and Kushwah, Virendra Singh},
  booktitle = {2024 2nd International Conference on Advances in Computation,
               Communication and Information Technology (ICAICCIT)},
  pages     = {624--629},
  year      = {2024},
  publisher = {IEEE},
  doi       = {10.1109/ICAICCIT64383.2024.10912101}
}
```

A [`CITATION.cff`](CITATION.cff) is included, so GitHub's "Cite this repository" button produces the same thing.

## Keywords

Context-aware bug detection · automated bug fixing · CI/CD integration · software development lifecycle · transformers · graph neural networks · automated program repair

## Licence

Documentation in this repository is released under [CC BY 4.0](LICENSE). The paper itself is © 2024 IEEE; read it on [IEEE Xplore](https://ieeexplore.ieee.org/document/10912101).
