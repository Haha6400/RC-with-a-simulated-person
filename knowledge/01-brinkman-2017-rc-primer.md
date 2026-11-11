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

- **Base image:** a neutral, average-looking face (often a morph of many faces), grayscale, the same for every trial. It keeps the stimuli face-like so that the judgements are meaningful.
- **Noise:** random patterns added on top of the base image. These are what actually vary between trials.
  - In `rcicr`, the default is **sinusoidal noise**: sinusoid patches summed over **5 spatial scales** (1, 2, 4, 8, 16 patches per side), **6 orientations** (0°, 30°, 60°, 90°, 120°, 150°) and **2 phases**, for **4,092 parameters** in total. Each parameter gets a random contrast weight from a uniform distribution on [−1, 1].
  - Superimposing several scales gives both coarse (global shape) and fine (local detail) variation.
- **Inverted noise:** each noise pattern is also used with its sign flipped (−noise). This gives the pair for the 2IFC trial and makes sure each pair is as different as possible.

### 2. Data acquisition: the 2IFC task

- **2-Image Forced Choice (2IFC):** on each trial, two images appear side by side (*base + noise* and *base − noise*). The participant picks the one that best fits the target (e.g. "more trustworthy", "more male", "more criminal").
- An alternative is rating single stimuli on a scale (1IFC with ratings). 2IFC is the common default because the choice is simple and fast.
- Typical length: several hundred trials per participant (300–1000). A response is recorded as which image was chosen (+1 for the original noise, −1 for the inverted one).

### 3. Computing the classification image

- **Individual CI:** average the chosen noise patterns. In practice: mean of (response × noise_i), where response ∈ {+1, −1}.
- **Group CI:** average the individual CIs (or all trials) across participants in a condition.
- **Visualisation:** the mean noise is **scaled**, because averaged noise has low contrast, and then **added to the base image**, so the result can be viewed as a face.
  - The choice of scaling matters. Use the **same scaling** across CIs you want to compare, or differences between them can be scaling artefacts.
- **Anti-CI:** subtracting the mean noise instead of adding it shows the *opposite* of the representation.

### 4. Evaluating CIs

- **Subjective validation:** a new, independent group of naïve raters judges the CIs on the trait of interest (e.g. rates the "trustworthy" CI against the "untrustworthy" one). This checks that the CI carries the intended social meaning.
- **Objective / statistical:** pixel-wise tests (e.g. z-maps or cluster tests on the noise) that show which regions of the CI differ reliably from noise or between conditions.
- **Caveat:** raters reading a CI is one more interpretation step. The CI is a *proxy* for the representation, not the representation itself.

---

## Key terms

| Term | Meaning |
|---|---|
| **Reverse correlation (RC)** | Inferring an internal template by correlating random stimulus variations with responses |
| **Classification image (CI)** | Average of the chosen noise (added to the base), a visual estimate of the mental representation |
| **Base image** | The constant face that noise is added to |
| **2IFC** | Two-image forced choice: original noise vs. inverted noise |
| **Sinusoidal noise** | `rcicr` default: summed sinusoid patches over scales, orientations and phases |
| **Anti-CI** | Base minus the mean noise, the opposite of the CI |
| **`rcicr`** | R package (Dotsch) for generating RC stimuli and computing CIs |

## Relevance to this project (RC with a simulated person)

- The **2IFC trial loop** is the interface the simulated participant has to implement: two images in, one choice out.
- The **base image + sinusoidal noise generator** is the stimulus side and can be reproduced from the `rcicr` parameters above.
- **CI computation** works the same whether the responder is human or simulated. If a simulated observer has a known internal template, we can check how well the CI recovers it.
