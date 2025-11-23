# Method

Section III of the paper, restated so it can be read without the PDF open.

## The shape of the problem

Let `C` be source code, `M` the metadata around it (architecture, dependencies, runtime conditions), and `B` the labelled bugs. The system learns to predict `B` and to produce fixes `F`, using a transformer `T` and a graph neural network `G`.

The thing to notice is that `M` is a first-class input, not a feature appended late. Everything downstream is built to carry it.

## 1. Preprocessing

Three views of the same code, because no single one carries all the signal.

**Tokens.** `C → T(C) = {t₁, t₂, … tₙ}`. The linear surface of the code, which is what a transformer reads well.

**Syntax.** `AST(C) = Tree(C)`. The hierarchy. A comparison inside a branch inside a loop is a different thing from the same comparison at module scope, and only the tree says so.

**Structure.** `G(C) = (V, E)`, where `V` is code modules and `E` the relationships between them. This is the view that makes interaction bugs visible at all. A defect that only exists because two modules disagree has no evidence inside either module's token stream.

## 2. Model input

Contextual data is assembled as:

```
X = [C, M]
```

and fed to each branch in the form it needs:

```
T_in = Embedding(X)      transformer branch
G_in = {V, E, M}         graph branch
```

`M` reaches both. The token branch gets it as part of the embedded input; the graph branch gets it attached to nodes. That redundancy is deliberate, since either branch alone should be able to notice when the environment is the reason something is wrong.

## 3. Training

Each branch produces a representation:

```
h_T = Attention(T_in)     transformer
h_G = Graph(G_in)         GNN
```

They are concatenated and scored:

```
B' = SoftMax(W[h_T, h_G] + b)
```

trained against the labels with cross-entropy:

```
L = CrossEntropy(B, B')
```

The concatenation is the whole architectural argument. Neither representation is privileged, and the classifier learns how much to weigh what the code says against who it talks to. Table III is the evidence that this is worth doing: 91.4% combined against 88.2% and 85.7% for the branches alone.

## 4. Fix generation

For a file flagged as buggy, a decoder conditioned on both representations proposes a patch:

```
F = Decoder(h_T, h_G)
```

Conditioning on both matters for the same reason detection does. A fix that satisfies the local file but breaks a caller is not a fix, and `h_G` is what carries the callers.

Generated patches are validated by unit tests and runtime analysis before they go anywhere. The system proposes; it does not merge on its own authority.

## 5. CI/CD integration

On detection, the validated fix is applied in the pipeline:

```
Deploy Fix(F)
```

This is where the practical result comes from. Table V shows average build time falling from 45.7 s to 32.2 s with the models integrated, detection rate rising from 65% to 91%, and automated fix success from 62% to 88%. The build getting faster is the part people find surprising. It is not mysterious: a defect caught during the build does not cost a second build to fix.

## Evaluation

Detection is measured with precision, recall, and F1. Efficiency is measured as wall-clock time to detect and to fix, compared against traditional static analysis.

## Reading the architecture critically

Three honest observations for anyone building on this.

**The graph branch is the weaker learner alone, and that is expected.** 85.7% against the transformer's 88.2%. Structure without content cannot distinguish a correct function from a broken one. Its value is entirely in what it adds to the combination, and the combined score is the only number that justifies its presence.

**The dependency graph assumes the code parses.** A syntax error is a bug, and it is also the case where the graph view is least available. Edit-based repair literature raises this same objection against graph-representation methods, and it applies here too.

**Runtime metadata is the hardest input to obtain.** Architecture and dependencies can be read from the repository. Runtime behaviour has to be observed in a live system, which is what makes the method powerful and also what makes it expensive to adopt on a codebase that is not already instrumented.
