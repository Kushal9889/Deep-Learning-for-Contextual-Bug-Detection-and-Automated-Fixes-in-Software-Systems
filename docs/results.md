# Results

Every number the paper reports, with what it does and does not establish.

## Detection quality

Table III. Accuracy, precision, recall, F1, in percent.

| Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|
| Transformer | 88.2 | 86.9 | 87.4 | 87.1 |
| Graph neural network | 85.7 | 84.2 | 85.1 | 84.6 |
| Combined | 91.4 | 90.8 | 91.1 | 91.0 |

**What this shows.** The combined model gains 3.2 points of accuracy over the transformer alone and 5.7 over the graph model. The gain is consistent across all four metrics, not concentrated in one, which is the pattern you want. A jump in recall with flat precision would suggest the combined model had simply become more willing to flag things.

**Reading the branches.** The graph model is the weakest alone, and it should be. Structure without content cannot separate a correct function from a broken one; it only knows who calls whom. Its entire justification is the 3.2 points it adds on top of the transformer. If that delta had been under a point, the second branch would not have earned its complexity.

**Precision and recall sit close together** in every row, within about half a point in the combined model. Bug datasets are heavily imbalanced, since most files are fine, so a model can score well on accuracy while being useless. These being balanced is the more informative signal.

## Time to detect and fix

Table IV, in seconds.

| Method | Detection | Fixing | Total |
|---|---|---|---|
| Traditional static analysis | 25.4 | 8.3 | 19.2 |
| Transformer | 12.3 | 6.5 | 18.8 |
| Combined | 8.7 | 4.8 | 13.5 |

**What this shows.** Detection time roughly a third of static analysis, fixing time roughly half.

**On row one.** Its total does not equal its two components, while rows two and three add up exactly. The paper's discussion compares the combined model's 13.5 s against 25.4 s for traditional static analysis, so 25.4 is the figure the narrative treats as the baseline. Noted here so a reader does not have to decide whether they misread the table.

**What it does not establish.** Wall-clock timings depend on hardware, project size, and how much runtime data has to be collected. The ratios are the transferable part; the absolute seconds are not.

## Inside a CI/CD pipeline

Table V.

| Pipeline | Average build (s) | Bug detection rate | Automated fix success |
|---|---|---|---|
| Without model integration | 45.7 | 65% | 62% |
| With model integration | 32.2 | 91% | 88% |

**The counterintuitive result.** Adding model inference to the pipeline made builds faster, 45.7 s down to 32.2 s. Adding work to a build should cost time. It does not here because a defect caught during the build does not cost the rebuild it would otherwise have triggered. The saving from prevented rebuilds exceeds the cost of inference.

**Fix success at 88%** is the number to weigh most carefully. It means roughly one generated patch in eight was wrong. Every patch is validated by unit tests and runtime analysis before use, so the failures are caught rather than merged, but a team adopting this needs the validation layer, not just the model.

**Detection rate rose from 65% to 91%.** That 65% baseline is what conventional pipeline tooling was catching, and the gap is the practical case for the method.

## What the numbers support, and what they do not

**Supported.** Contextual metadata improves detection. The two representations are complementary. The approach is fast enough to sit inside a build.

**Not supported by these tables.** Any claim about a specific language or framework beyond those evaluated. Any claim about behaviour on codebases without existing bug annotations. Any claim that the absolute timings hold on different hardware.

The paper states these limits directly. They are reproduced in the README rather than left for a reader to infer.
