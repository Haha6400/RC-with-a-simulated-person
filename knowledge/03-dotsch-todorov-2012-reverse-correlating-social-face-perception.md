# 03 — Dotsch & Todorov (2012): Reverse Correlating Social Face Perception

> **Citation:** Dotsch, R., & Todorov, A. (2012). Reverse correlating social face perception. *Social Psychological and Personality Science, 3*(5), 562–571.

| | |
|---|---|
| **What to read** | Skim it as an example |
| **Why** | What a real RC study and its results look like |
| **Role in project** | Worked example: the standard 2IFC + sinusoidal-noise pipeline from [01 — Brinkman et al., 2017](01-brinkman-2017-rc-primer.md), applied to social traits, with the validation methods we can reuse |

---

## I. Problem and motivation

- People judge social traits from faces **almost instantly**, but **cannot say which facial features they use**:
  - some features have no names;
  - people may be unaware of their own cues;
  - the hypothesis space explodes: 15 binary features already give **32,768** combinations, and 20 give **over a million**.
- **RC is a data-driven way around this:** vary the stimuli randomly and find out which variations predict the judgement.

**Earlier approach: face-space RC (Oosterhof & Todorov, 2008).** Randomly generated 3D model faces were rated, and trait directions were built as linear combinations of face-space dimensions. Four limitations:

| # | Limitation |
|---|---|
| 1 | Results depended on the **face model** (a PCA of 271 laser-scanned faces) |
| 2 | Faces had **no hair** |
| 3 | Only **shape** was modelled, not skin texture or pigmentation |
| 4 | No **quantitative test** of which face regions matter |

---

## II. Method

| Component | Details |
|---|---|
| **Participants** | 80 Princeton students. Each did **one** task: trustworthy, untrustworthy, dominant or submissive. **2 (dimension) × 2 (pole) between-subjects**, 20 people per cell. |
| **Base face** | Grey-scale **average of the male faces in the Karolinska database**, 512 × 512 px |
| **Noise** | Sinusoid patches: **6 orientations × 2 phases × 5 spatial scales** (2, 4, 8, 16, 32 cycles per image), each with a random contrast → **4,092 parameters** |
| **Task (2IFC)** | *base + noise* and *base − noise* side by side; pick the face that looks more trustworthy (or the assigned trait). Negative noise was used to make the two images **as different as possible**, **reduce the number of possible pairs**, and **simplify the analysis**. |
| **Trials** | **300 per person**. Two different sets of 300 noise patterns were used, so results don't depend on one particular set. Left/right position counterbalanced. |
| **CI** | Average each person's **selected** noise parameters → average across the 20 people in a cell → overlay on the base. |
| **Anti-CI** | Same procedure applied to the **unselected** images. |

---

## III. Results

### a. What the CIs look like

| Trait | Visible features |
|---|---|
| **Trustworthy** | Small, smooth face; smiling mouth; open eyes |
| **Untrustworthy** | Down-turned mouth with thick lips; angry eyes; sagging cheeks; a bald spot |
| **Dominant** | Clear eyebrows; dark eyes; slightly down-turned mouth; face stands out from the background |
| **Submissive** | Frowning; thin lips; sad eyes; face blends into the background |

### b. Agreement between participants

**Cronbach's α** over the pixels of the individual CIs within each cell: **.56 to .76**, i.e. fair agreement.

| Trustworthy | Untrustworthy | Dominant | Submissive |
|---|---|---|---|
| .56 | .65 | .76 | .58 |

### c. Objective metric: correlations between CIs

| Pair | r | Reading |
|---|---|---|
| Trustworthy vs untrustworthy | **−.65** | Opposites |
| Dominant vs submissive | **−.70** | Opposites |
| Trustworthy vs dominant | −.27 | Weak |
| Untrustworthy vs dominant | .50 | |
| Trustworthy vs submissive | .23 | |
| Untrustworthy vs submissive | −.41 | |
| CI vs anti-CI of the opposite pole | .66, .67, .71, .71 | Anti-CI looks like the opposite trait |

- Opposite poles are **physically opposite**.
- The two dimensions are **mostly distinct but not fully independent**.

### d. Subjective metric: ratings by new people

- **38 new students** rated the four group CIs (1–9) on **trustworthiness, dominance and threat**.
- Each CI scored **highest on its intended trait**.
- **Threat** was predicted best by trustworthiness **and** dominance together (**R² = .73**), better than trustworthiness alone (.41) or dominance alone (.64). This matches the earlier face-space model, where **threat ≈ untrustworthy + dominant**.

### e. Which face regions matter

A **pixel-cluster test** (Chauvin et al., 2005), with Gaussian smoothing, an oval face mask and **|Z| > 2.3**, located the regions that drive each judgement:

| Judgement | Significant regions |
|---|---|
| **Trustworthiness** | Mouth, eyes, eyebrows, hair |
| **Dominance** | Eyes, hair, outline of the face |

**Hair** had been missing from the earlier face-space models.

---

## IV. Discussion and conclusions

- 2IFC RC can visualise **social traits** beyond gender, identity and emotion, with **no assumptions** about what the representations look like.
- The results **converge with** the face-space model and **extend** it (hair, figure–ground contrast).
- The small overlap between the two dimensions may reflect:
  - the **fixed order of rating blocks**, or
  - the **"compensation" effect** (people seen as high on one dimension are seen as low on the other).
- **CIs are not the actual mental representations.** They are approximations shaped by the **task, the base image and the noise**.
- Possible applications: cultural differences, biases about social groups, memory biases, eyewitness facial composites.

---

## V. Critical-reading notes, and why they matter for this project

| Note | Why it matters |
|---|---|
| **Group-level CIs, not individual ones** | CIs were averaged over 20 people because 300 trials per person in a 4,092-dimensional space gives a very noisy individual CI. The project needs **individual templates**, which is exactly where RC is weakest. <br>*Họ phải gộp 20 người vì 300 lượt mỗi người là quá ít để có ảnh rõ. Dự án cần ảnh cho từng người, và đó chính là điểm yếu của RC mà dự án muốn cải thiện.* |
| **The anti-CI result is largely built into the design** | With ±n pairs, the unselected noise is exactly the negative of the selected noise, so **anti-CI = −CI by construction**. The CI vs anti-CI correlations (.66, .71) are then almost the mirror of the CI vs CI correlations (−.65, −.70). The small differences are probably rounding or masking (**not checked**). The anti-CI "validation" therefore adds little independent evidence. <br>*Vì ảnh không được chọn luôn là "âm bản" của ảnh được chọn, nên anti-CI tự động là ngược của CI. Kết quả này gần như do thiết kế quyết định, không phải một phát hiện mới.* |
| **Reusable validation methods** | Agreement between people (α), correlations between CIs, ratings by naïve judges, pixel-cluster tests. In **Study 2**, comparing an IEC estimate against the RC CI is the same kind of objective-correlation check. |
| **Effective dimension is 4,092, not 512 × 512** | The noise is low-frequency sinusoids. This is a step toward the **lower-dimensional latent spaces** the project plans to use. |
| **Single base face** | All results are relative to one average male face. The **ideal-point and base-dependence** issues from [02 — Murray, 2011](02-murray-2011-classification-images-review.md) apply here too. |
| **Historical link** | The authors credit this class of RC partly to **Kontsevich & Tyler (2004)**, the same researchers behind the **psi adaptive method (1999)** that the proposal cites. The two lines of work share a common origin. |

---

## Key terms

| Term | Meaning |
|---|---|
| **Face-space RC** | RC over the parameters of a 3D face model (Oosterhof & Todorov, 2008), instead of over image noise |
| **Karolinska database** | Face database (KDEF) whose male average was used as the base face |
| **Cronbach's α (over pixels)** | Agreement between individual CIs within a cell, treating participants as "items" |
| **Pixel-cluster test** | Chauvin et al. (2005) test for contiguous regions of significant pixels (here \|Z\| > 2.3) |
| **Compensation effect** | Tendency to see someone high on one social dimension as low on the other |
