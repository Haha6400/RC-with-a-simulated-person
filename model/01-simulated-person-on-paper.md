# Step 2 — The simulated person, on paper

The model the code will implement, written as equations before any code. It is the linear observer from [Murray (2011)](../knowledge/02-murray-2011-classification-images-review.md) running the 2IFC task from [Brinkman et al. (2017)](../knowledge/01-brinkman-2017-rc-primer.md), in a small abstract space (d = 10) instead of pixels.

---

## 1. Notation

| Symbol | Meaning |
|---|---|
| **d** | Number of dimensions of the stimulus space, i.e. how many numbers describe one noise pattern. Start with d = 10 (`rcicr` uses 4,092; a pixel image uses 512 × 512). |
| **θ** | The simulated person's **hidden template**: the direction in stimulus space that they find "more trustworthy". A unit vector (‖θ‖ = 1) that we draw at random, keep hidden from the estimator, and use as ground truth. |
| **n** (or **n_t**) | The **noise pattern** on trial t: a vector of d random numbers, n ~ N(0, I). The two images on screen are *base + n* and *base − n*. |
| **ε** (or **ε_t**) | **Internal noise**: random wobble inside the person on trial t, ε ~ N(0, σ²). It explains why the same stimulus does not always get the same answer. |
| **σ** | The **size of the internal noise**. Because ‖θ‖ = 1, θᵀn ~ N(0, 1), so σ is directly the ratio of internal noise to the stimulus-driven part of the decision. σ = 0 is a perfectly consistent person; σ = 1 means half the decision variance comes from inside the person. |
| **s** (or **s_t**) | The **response** on trial t: s = +1 if they picked *base + n*, s = −1 if they picked *base − n*. Equivalently, s_t·n_t is the noise in the image they chose. |
| **N** | The **number of trials**. |
| **CI** | The **classification image**: our estimate of θ, built only from the noise patterns and the responses. |

---

## 2. Data-generating process

```
1. θ ← random unit vector in ℝᵈ               (once per simulated person)
2. for t = 1 … N:
       n_t ~ N(0, I_d)                       (new noise pattern)
       ε_t ~ N(0, σ²)                        (new internal noise)
3.     the person chooses base + n_t  if  θᵀn_t + ε_t > 0
                          base − n_t  otherwise
4.     record s_t = +1 or −1
```

To draw θ: take g ~ N(0, I_d) and set θ = g / ‖g‖. This gives a direction that is uniform over the sphere.

**Why the rule in step 3 is the 2IFC task.** A linear person scores each image as θᵀ(image) plus their own noise, and picks the higher score:

```
θᵀ(b + n) + ε₁  >  θᵀ(b − n) + ε₂   ⇔   2θᵀn + (ε₁ − ε₂) > 0   ⇔   θᵀn + ε > 0
```

with ε = (ε₁ − ε₂)/2. The **base b cancels out**, so for a linear observer the base does not matter, and the model does not need to represent it. (This stops being true for a nonlinear observer; see the ideal-point problem in Murray.)

---

## 3. Estimator

```
CI = Σ_{t=1}^{N} s_t · n_t
```

- The sum of the noise in every chosen image.
- Dividing by N (the mean, as in [00-overview](../knowledge/00-overview.md)) or scaling for display changes only the length, not the direction, so it does not affect the metric below.

**Why it works.** Split each noise pattern into a part along θ and a part perpendicular to it: n = (θᵀn)θ + n_⊥. The response depends only on θᵀn and ε, so it is independent of n_⊥.

- Along θ: s_t and θᵀn_t tend to have the same sign, so these terms **accumulate**.
- Perpendicular to θ: s_t·n_⊥ is + on some trials and − on others, so these terms **cancel**.
- Hence E[CI] = N·a·θ, with a = E[θᵀn · s] = √(2/π) / √(1 + σ²).

---

## 4. Metric

```
accuracy = cos(CI, θ) = (CIᵀθ) / (‖CI‖ · ‖θ‖)
```

- 1 = the CI points exactly at the template. 0 = it is unrelated.
- It measures **direction only**. That is all we can claim, because the person's choices depend only on θ/σ, so the strength of θ cannot be separated from σ (Murray's w/σ_z point).
- Chance level: for a pure random guess in d = 10 dimensions, the **average cos is ≈ 0** (positive and negative are equally likely), the average |cos| is ≈ 0.26, and the RMS is 1/√d ≈ 0.32. So **0.32 is the typical spread of a random guess, not its average.**

---

## 5. Assumptions

1. **Fixed, unit-length template.** The person has one template θ, ‖θ‖ = 1, that does not change across trials (no learning, fatigue or drift).
2. **Isotropic Gaussian noise.** Each n_t ~ N(0, I) is drawn fresh and independently on every trial. Every direction in the space is equally likely, which is what the geometric argument for E[CI] ∝ θ needs.
3. **Linear, unbiased decision rule.** The person compares θᵀn with 0: no criterion shift and no left/right or +/− preference.
4. **Gaussian internal noise, independent of the stimulus.** ε_t ~ N(0, σ²), with constant σ, independent of n_t and across trials.

Under these four assumptions E[CI] ∝ θ, so the CI is an unbiased estimate of the template's direction.

---

## 6. Predictions before running the code

From the decomposition in §3, the length of the CI along θ is about N·a, and the perpendicular part has length about √(N(d − 1)). This gives an approximation (the same one in [00-overview](../knowledge/00-overview.md)):

```
cos(CI, θ) ≈ 1 / √(1 + (d − 1) / (N·a²)),   a² = (2/π) / (1 + σ²)
```

| # | Prediction | What to check in code |
|---|---|---|
| P1 | **More trials → higher cosine.** At N = 1 the average cosine is only about 0.18 (a pure random guess averages 0, with typical spread ±1/√d ≈ ±0.32). It then rises toward 1, quickly at first and then slowly. | Plot mean cos vs N for d = 10, σ = 1. Expect ≈ 0.18 at N = 1 and ≈ 0.96 at N = 300. |
| P2 | **Larger σ → more trials needed.** Trials needed for a fixed cosine scale with (1 + σ²). Going from σ = 1 to σ = 2 multiplies the required N by 2.5. | Repeat P1 for σ ∈ {0, 0.5, 1, 2} and compare where each curve reaches 0.9. |
| P3 | **Larger d → more trials needed.** Trials needed scale with (d − 1). Going from d = 10 to d = 100 multiplies the required N by 11. | Repeat P1 for d ∈ {10, 100, 1000}. |

Predicted trials needed to reach cos = 0.9 (from N = (d − 1)/a² · c²/(1 − c²)):

| d | σ = 0 | σ = 0.5 | σ = 1 | σ = 2 |
|---|---|---|---|---|
| 10 | ≈ 60 | ≈ 75 | ≈ 120 | ≈ 300 |
| 100 | ≈ 660 | ≈ 830 | ≈ 1,330 | ≈ 3,320 |

**These predictions are about the average over many simulated persons, so averaging is required, not optional.** At small N, one person's cosine varies a lot (SD ≈ 0.27 at N = 1); only at N = 300 does the SD drop to about 0.02. A single simulated person can therefore land far from the prediction by chance.

- Always run **many simulated persons** (e.g. 200), each with its own θ, noise and ε.
- Plot the **mean ± SD** of the cosine across persons, never a single run.
- The mean should fall within about 0.02 of the predicted values. A bigger gap in the mean means a bug or a broken assumption.

---

## 7. From equations to code

| Equation | Function |
|---|---|
| θ random, length 1 | `make_template(d)` |
| n ~ N(0, I) | `make_noise(n_trials, d)` |
| choose base + n if θᵀn + ε > 0 | `observer_choose(images, theta, sigma, k=1)` → boolean mask of the chosen images. General k-of-n rule: score each image u_i = θᵀx_i + η_i with **η_i ~ N(0, sigma²)** (per-image σ), and pick the top k, breaking tied scores at random. Images are stacked as `[base + n, base − n]`; `choices_to_sign(chosen)` gives s_t = +1 if column 0 (b + n) was chosen, else −1. |
| CI = Σ s·n | `classification_image(noise, choices)`, with `choices` = s_t ∈ {+1, −1} |
| cos(CI, θ) | `cosine(a, b)` |

**σ in this document vs. `sigma` in the code.** This document's σ is the noise in the 2IFC rule θᵀn + ε > 0. The code's `sigma` is the noise on **each image's score**. For a pair b ± n, u₁ > u₂ ⇔ θᵀn + (η₁ − η₂)/2 > 0, so

```
σ (this document) = sigma (code) / √2        sigma (code) = √2 · σ (this document)
```

To check the §6 predictions, either run the code with `sigma = √2 · σ`, or use a² = (2/π) / (1 + sigma²/2) in the formula.

Each function that draws random numbers takes an `rng` argument (None, an int seed, or a `np.random.Generator`, passed to `np.random.default_rng`), so that runs are reproducible.

The experiment loop sits on top of these functions: repeat the whole pipeline for **n_persons** simulated persons (e.g. 200) at each (N, d, σ) setting, then report the mean and SD of the cosine (see §6).
