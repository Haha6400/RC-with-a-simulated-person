# 01 — Brinkman et al. (2017): Noise-based Reverse Correlation Primer

> **Citation:** Brinkman, L., Todorov, A., & Dotsch, R. (2017). Visualising mental representations: A primer on noise-based reverse correlation in social psychology. *European Review of Social Psychology, 28*(1), 333–361.

| | |
|---|---|
| **What to read** | The procedure sections: 2IFC task, base image, noise, computing the classification image |
| **Why** | How RC is done in practice, in our application area (faces, social traits) |
| **Role in project** | Base knowledge: defines the experimental pipeline that the simulated person will take part in |

---

## I. Introduction

### Context

- How a person is seen "in the mind's eye" (one's subjective experience of that person) depends on the content of **mental representations**.
- **Reverse correlation** (also called the **classification image technique**) produces *visual proxies* of the content of those representations.
- **Why "reverse"?** The usual procedure, where the experimenter puts a signal into the stimuli and participants try to identify it, is turned around. Here the stimuli are random, and the "signal" is recovered from what participants choose.

### Basic RC paradigm: workflow

1. **Generate stimuli.** Participants see a large set of variations within one stimulus class.
   - Example: a **base image** (usually a neutral face) plus **random noise**. Each pair = *base + noise* and *base + inverted noise* (đảo dấu, i.e. the same noise with its sign flipped).
2. **Judge.** Participants rate each variation on how similar it is to a mental representation of interest (e.g. "Which face looks more trustworthy?").
3. **Repeat.** Participants complete **300–1000 trials**.
4. **Compute the CI.** The noise patterns behind the participants' judgements are used to compute a model, the **classification image (CI)**.
   - Lấy trung bình (có thể có trọng số) các mẫu nhiễu được chọn: average the chosen noise patterns, possibly weighted.
   - The CI shows which features drive the judgement, so it is treated as an **approximation of the mental representation**.

```
base face ──┬── + noise_i  ──┐
            └── − noise_i  ──┴─► participant picks one ─► store chosen noise
                                     (× 300–1000 trials)
                                              │
                         mean(chosen noise) ──┴─► CI = base + scaled mean noise
```

### Why RC beats traditional (hypothesis-driven) paradigms

| Advantage | Explanation |
|---|---|
| **Data-driven** | A face can vary along many features. Even if researchers find a set of features linked to a judgement, they can't be sure without more work that it is the *only* or *strongest* predictor. Random variation tests **no single hypothesis**. Instead it samples a **whole space of hypotheses**, spanned by the variations in the stimulus set. |
| **Visualises near-spontaneous use of information** | Participants can use any criteria they like for their judgements, and may not even be aware of the criteria they use. |

### Paper structure

1. Method, plus an **`rcicr` R package** tutorial for creating stimuli and analysing RC data
2. Major achievements of the technique in social psychology
3. Future directions and limitations

---

## II. Methodological Details

The pipeline has four stages:

1. **Construct stimuli** with random variations
2. **Acquire data**: participants select the images that match their mental representation
3. **Compute CIs** from the participants' responses
4. **Evaluate CIs**

### 1. Stimulus construction

Two main decisions: **which base image** and **what type of noise**.

#### a. Base image

- Usually an **average face** from a face database, the same on every trial. It keeps the stimuli face-like so that judgements are meaningful.
- The base face should:
  - **Fit the research question** (e.g. right gender, age or ethnicity for the target representation).
  - **Match the power spectrum of the noise** as well as possible, e.g. with a low-pass filter or Gaussian blur. In practice the face contours are often blurred while the inner features (eyes, nose, mouth) stay in focus.
- **Resolution:** higher resolution needs more trials. The face should fill most of the image.
- **Sine-wave noise constraint:** the image must be **square**, with a side length that is a **power of 2** (e.g. 256 or 512 px).
- **Pitfall:** an unsuitable base image leads to **insufficient sampling** of the space. E.g. if the base face has a closed mouth, it is hard to recover a representation that needs an open mouth.

#### b. Type of noise

| Noise type | Parameters | Example and trials needed | Pros | Cons |
|---|---|---|---|---|
| **White noise, no base image** | One per pixel (e.g. 50×50 = **2,500**) | Detecting the letter **"S"** in pure noise: about **20,000 trials** | No constraints on what can emerge | Very many trials, even though an "S" is a much simpler target than a face |
| **White noise on a base image** | One per pixel | Facial expressions of emotion in Western Caucasian vs. East Asian participants: about **12,000 trials per person** | CIs showed clear differences between the two groups | Trial count is still very high |
| **Sine-wave noise** (`rcicr` default) | **4,092** (superimposed sine-wave patches; see below) | **Most popular**: about **300–1000 trials** | Few parameters; matches the spatial-frequency spectrum of faces; samples efficiently the parts of stimulus space that matter for faces | Cannot represent every kind of visual information (only what the sine-wave basis can express) |
| **Gabor noise** | **16,380** (6 layers) | About **3,240 trials per person** | Built the same way as sine-wave noise but with **Gabor patches** instead of sine-wave patches | Not yet systematically compared with sine-wave noise |

**How the 4,092 sine-wave parameters arise:**

- **5 spatial scales (layers)**: the image is divided into 1×1, 2×2, 4×4, 8×8 and 16×16 patches, giving 1 + 4 + 16 + 64 + 256 = **341 patches**.
- In each patch: **6 orientations** (0°, 30°, 60°, 90°, 120°, 150°) × **2 phases** = **12 sine waves**.
- 341 × 12 = **4,092** parameters. Each gets a random contrast weight from a uniform distribution on [−1, 1].
- Stacking several scales gives both coarse (global shape) and fine (local detail) variation.

**Inverted noise:** each noise pattern is also used with its sign flipped (−noise). This gives the pair for a 2IFC trial and makes each pair as different as possible.

### 2. Data acquisition

When setting up an RC task, decide: **how many stimuli are shown at once**, **how many response options** there are, and **how many trials**. Two common designs:

| | **2IFC** (two-image forced choice) | **4AFC** (four-alternative forced choice) |
|---|---|---|
| **Presentation** | 2 images side by side; the noise in one is usually the mathematical inverse of the other (*base + noise* vs. *base − noise*) | 1 image at a time |
| **Response** | Pick the image that best fits the mental representation (e.g. "more trustworthy") | Rate the image on a 4-point scale on one (or more) dimensions of interest (e.g. *probably not / possibly not / possibly / probably*) |
| **Data used for the CI** | **All trials** | Only images rated **"probably"**; the "possibly" ratings are dropped |
| **Pros** | Every trial carries information, even when the base image is far from the target | Participants can express **certainty**, which can be used when computing the CI |
| **Cons** | No certainty judgements | If the base is far from the target, there may be **no "probably" ratings at all**, and then no CI can be computed |

- A 2IFC response is recorded as which image was chosen: **+1** for the original noise, **−1** for the inverted one.
- Typical length: **300–1000 trials** per participant.

### 3. Computing the classification image

The CI visualises the parameter estimates that participants treat as relevant when classifying the stimuli.

#### a. From 2IFC data

- **Average the chosen noise patterns**: mean of (response × noise_i), with response ∈ {+1, −1}. Then **superimpose** this average noise on the base image.
- The average noise values are **very small**, because averaging patterns that are mostly random cancels most of them out and leaves little signal.
- So the average noise is **scaled** so that the minimum and maximum pixel intensities of the resulting CI match those of the base image.

#### b. Scaling options

*(Section left empty in the notes; filled from the paper and `rcicr` docs. Check against the original.)*

- **Matched scaling:** stretch each CI's noise to the base image's intensity range (the default described above).
- **Independent scaling:** scale each CI on its own range.
- **Constant scaling:** multiply all CIs by the **same constant**.
- **Rule:** when CIs will be **compared** (e.g. between conditions), use the **same scaling for all of them**, or the differences can be scaling artefacts.

#### c. From 4AFC data

- Same logic as 2IFC: average the noise of the selected ("probably") stimuli, scale it, and add it to the base image.

#### d. Group CI

- Average the **unscaled** noise patterns behind the individual CIs across the participants in a group or condition, then scale the result.
- **Assumption:** the mental representation is **homogeneous across participants**, which is not always true.

#### e. Anti-CI

- Subtracting the average noise instead of adding it shows the *opposite* of the representation.

### 4. Evaluating CIs

| Method | How | Notes |
|---|---|---|
| **Independent raters** | A separate group of naïve raters judges the CIs on specific criteria (e.g. how trustworthy the "trustworthy" CI looks vs. the "untrustworthy" one) | Set the criteria **before** anyone views the CIs, and require high **inter-rater agreement**. With many raters even tiny differences can become "statistically significant", especially with independent rating scales, which can amplify noise |
| **Correlation with a reference CI** | E.g. to check the reliability of a "male face" CI, build a reliable reference CI from the same underlying data and correlate the two (Imhoff & Dotsch, 2013) | More objective than human raters |
| **Pixel / z-map inspection** | Test individual pixels and build a **z-score map** to find which face regions drive the judgements | Needs **no a priori hypotheses** |
| **infoVal** | Measures how much signal a CI contains compared with CIs built from **simulated random responses** (same stimuli, random choices); read as a **z-score** | **infoVal < 1.96** means no detectable signal, so the CI should be discarded. Available in **`rcicr` ≥ 0.4.0** |

**Caveat:** raters reading a CI is one more interpretation step. The CI is a *proxy* for the representation, not the representation itself.

---

## Key terms

| Term | Meaning |
|---|---|
| **Reverse correlation (RC)** | Inferring an internal template by correlating random stimulus variations with responses |
| **Classification image (CI)** | Average of the chosen noise (added to the base), a visual estimate of the mental representation |
| **Base image** | The constant face that noise is added to |
| **2IFC** | Two-image forced choice: original noise vs. inverted noise |
| **4AFC** | Four-alternative forced choice: one image rated on a 4-point scale |
| **Gabor noise** | Like sinusoidal noise but with Gabor patches (16,380 parameters) |
| **infoVal** | z-score of a CI's signal against CIs from random responders; < 1.96 = no signal |
| **Sinusoidal noise** | `rcicr` default: summed sinusoid patches over scales, orientations and phases |
| **Anti-CI** | Base minus the mean noise, the opposite of the CI |
| **`rcicr`** | R package (Dotsch) for generating RC stimuli and computing CIs |

## Relevance to this project (RC with a simulated person)

- The **2IFC trial loop** is the interface the simulated participant has to implement: two images in, one choice out.
- The **base image + sinusoidal noise generator** is the stimulus side and can be reproduced from the `rcicr` parameters above.
- **CI computation** works the same whether the responder is human or simulated. If a simulated observer has a known internal template, we can check how well the CI recovers it.
- **infoVal** gives a ready-made check for the simulated observer: a random responder should score < 1.96, and a template-driven one should score above it.
