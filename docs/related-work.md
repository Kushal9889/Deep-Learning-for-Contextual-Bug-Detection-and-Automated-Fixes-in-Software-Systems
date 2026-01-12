# Related work

The twenty-two references from the paper, grouped by what each contributes rather than listed in citation order. The paper's Tables I and II do a version of this for the first ten; this covers all of them.

## Context-aware detection

The line of work this paper extends.

**[1] Zhang, Lei, Mao, Yan, Xia, Lo (2023).** "Context-Aware neural fault localization." *IEEE Transactions on Software Engineering* 49(7), 3939–3954. [doi:10.1109/TSE.2023.3279125](https://doi.org/10.1109/TSE.2023.3279125)
A neural fault localiser that folds project architecture into the model. The direct precedent for using architectural context to improve localisation accuracy.

**[4] Han, Huang, Liu (2024).** "bjCnet: A contrastive learning-based framework for software defect prediction." *Computers & Security* 145, 104024.
Contrastive learning over runtime and project metadata. Evidence that transformers can carry contextual relationships, not just token sequences.

**[7] Chafjiri, Legg, Hong, Tsompanas (2024).** "Vulnerability detection through machine learning-based fuzzing: A systematic review." *Computers & Security* 143, 103903.
Runtime data combined with deep learning for detection. Supports including runtime conditions as model input.

## Graph representations of code

Why the GNN branch exists.

**[2] Cheng, Chen, Cao, Wang (2024).** "A vulnerability detection framework with enhanced graph feature learning." *Journal of Systems and Software* 216, 112118.
GNN capturing structural relationships between code elements.

**[10] Putrama, Martinek (2023).** "Integrating platforms through content-based graph representation learning." *International Journal of Information Management Data Insights* 3(2), 100200.
Graph representations for interactions in distributed systems, which is the setting where interaction bugs actually bite.

**[12] Dinella et al. (2020).** "Hoppity: Learning graph transformations to detect and fix bugs in programs." *ICLR 2020.*
Detection and repair as learned graph transformations. One of the anchors of the edit-based tradition.

**[20] Tarlow et al. (2020).** "Learning to fix build errors with graph2diff neural networks." *ICSE 2020 Workshops.*
Build errors to diffs, directly relevant to the CI/CD framing.

## Neural program repair

The competing formulation, and its known weakness.

**[11] Chen et al. (2019).** "Sequencer: Sequence-to-sequence learning for end-to-end program repair." *IEEE TSE* 47(9), 1943–1959.
The canonical seq2seq framing: buggy code in, fixed code out.

**[14] Jiang, Lutellier, Tan (2021).** "Cure: Code-aware neural machine translation for automatic program repair." *ICSE 2021.*

**[15] Lutellier et al. (2020).** "Coconut: Combining context-aware neural translation models using ensemble for program repair." *ISSTA 2020.*

**[19] Ye, Martinez, Monperrus (2022).** "Neural program repair with execution-based backpropagation." *ICSE 2022.*
Execution signal in the training loop rather than only static labels.

The paper's objection to this family is specific and worth keeping in view: buggy and fixed code overlap heavily, so a model trained to emit the fixed version learns to copy. Copying scores well and edits nothing.

## Edit-based repair

**[13] Hashimoto et al. (2018).** "A retrieve-and-edit framework for predicting structured outputs." *NeurIPS 31.*

**[16] Yao (2021).** "Learning structural edits via incremental tree transformations." arXiv:2101.12087.

**[17] Yin et al. (2018).** "Learning to represent edits." arXiv:1810.13337.

**[18] Jiang et al. (2023).** "Knod: Domain knowledge distilled tree decoder for automated program repair." *ICSE 2023.*

Shorter outputs than full-file generation, at the cost of multi-step machinery and, in the graph-based variants, an assumption that the broken code parses.

## Defect prediction and surveys

**[6] Sharma et al. (2024).** "A survey on machine learning techniques applied to source code." *Journal of Systems and Software* 209, 111934.
The state of the art survey the paper positions against.

**[8] Giray, Bennin, Köksal, Babur, Tekinerdogan (2023).** "On the use of deep learning in software defect prediction." *Journal of Systems and Software* 195, 111537.
End-to-end prediction plus fix generation.

**[9] He et al. (2024).** "Enhancing deep learning vulnerability detection through imbalance loss functions: An empirical study." *APSEC 2024.*
Class imbalance, which is the practical reality of bug datasets: most files are fine.

## Self-healing systems

**[5] Aldrini, Chihi, Sidhom (2023).** "Fault diagnosis and self-healing for smart manufacturing: a review." *Journal of Intelligent Manufacturing.*
Self-healing from an adjacent domain, framing the longer-term goal.

## Adjacent methods

**[3] Xu, Wang, Skidmore, Lamprey (2024).** "A review of deep learning techniques for detecting animals in aerial and satellite images." *International Journal of Applied Earth Observation and Geoinformation* 128, 103732.
Cited for the CI/CD and real-time integration pattern rather than the domain.

**[21] Nayak et al. (2023).** "Stock price prediction using LSTM and SVM." *SoCTA 2022*, Springer.
LSTM and SVM combined, cited as precedent for pairing deep and classical learners.

**[22] Parashar, Kushwah, Rai (2023).** "Determination human behavior prediction supported by cognitive computing-based neural network." *SoCTA 2022*, Springer, 431–441.
Cognitive-computing neural networks: detection plus continual learning and developer assistance, beyond pattern matching alone.
