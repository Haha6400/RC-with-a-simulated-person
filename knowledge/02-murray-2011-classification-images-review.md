# 02 — Murray (2011): Classification Images, A Review

> **Citation:** Murray, R. F. (2011). Classification images: A review. *Journal of Vision, 11*(5):2, 1–25.

| | |
|---|---|
| **What to read** | The linear observer model, how classification images are estimated, and internal noise / efficiency |
| **Why** | The theory: why the averaged noise estimates the template |
| **Role in project** | Theoretical base: justifies (and limits) the estimators the simulated-person pipeline will use |

---

## What the paper is

- A review of **15 years of classification-image (CI) methods** in visual psychophysics: where the method came from, how to estimate CIs, how to test them, and what to do when observers are **not linear**.
- It is about **low-level vision** (gratings, letters, contours), not social faces. The statistics are the same ones behind face RC (see [01 — Brinkman et al., 2017](01-brinkman-2017-rc-primer.md)).

---

## I. The standard method and two intuitions

**Typical experiment:** on each trial one of two signals (S1 or S2) is shown in Gaussian noise, and the observer says which one it was.

**Ahumada's (1996) standard CI** sorts trials into four groups, by *signal shown × response given*:

| | Responded 1 | Responded 2 |
|---|---|---|
| **S1 shown** | n̄₁₁ | n̄₁₂ |
| **S2 shown** | n̄₂₁ | n̄₂₂ |

```
CI = (n̄₁₂ + n̄₂₂) − (n̄₁₁ + n̄₂₁)
     └─ mean noise, "responded 2" ─┘   └─ mean noise, "responded 1" ─┘
```

Two ways to think about it:

| Intuition | Idea |
|---|---|
| **Averaging argument** | Noise that pushes the observer toward a response looks like the template for that response. |
| **Correlation map** | The CI is roughly the correlation between each pixel's noise and the response. Large values mark pixels that influenced the decision; values near zero mark pixels that did not. |

---

## II. History

| Period / field | Contribution |
|---|---|
| **Systems theory** | **Volterra and Wiener kernels** describe a complex system as a sum of first-order (linear), second-order (pairwise) and higher terms, like a "Taylor series with memory". **Lee & Schetzen (1965)** showed the kernels can be measured by feeding the system **white noise** and correlating input with output. The **first-order kernel is essentially a classification image.** |
| **Neurophysiology** | Applied quickly: de Boer & Kuyper (1968); Marmarelis & Naka (1972, catfish retina). In practice only kernels **up to second order** can be estimated, because the number of quantities explodes with order. |
| **Auditory psychophysics** | **Ahumada & Lovell (1971)** regressed confidence ratings on noise energy at each frequency. Auditory researchers call the result **"weights"**. |
| **Vision** | **Ahumada and Beard (1996–1998)** developed the standard method in a Vernier task. |

---

## III. The linear observer model

The observer computes a **decision variable**

```
d = wᵀ(signal + noise) + z
```

- **w** = the template
- **z** = internal noise
- Response is given by whether **d** exceeds a **criterion**.

Key properties:

1. **Identifiability:** only the **difference between the two templates** can be recovered. With *m* response options, only the differences between templates are identifiable.
2. **Unbiasedness (geometric argument):** the decision boundary is a **hyperplane perpendicular to w**. Gaussian noise is **spherically symmetric**, so the average noise on each side of the boundary is shifted along w. Hence:

   ```
   E[CI] ∝ w
   ```

   ```
            noise space
       ·  ·  · │ ·  ·  ·
     ·  ·  ·   │  ·  · ·      mean of "responded 2" noise  ●──►
       ·  ●◄───┼───►●  ·      mean of "responded 1" noise ◄──●
     ·  ·  ·   │  ·  · ·
       ·  ·  · │ ·  ·  ·      both means shift along w
               │
        decision boundary ⟂ w
   ```

3. **Robust to internal noise:** this still holds with internal noise, **as long as z is independent of the stimulus noise**.
4. **Signal choice doesn't matter (for a linear observer):** the experimenter's choice of signals does not affect the estimate. **This stops being true once the observer is nonlinear.**
5. **What the model leaves out:** transducer nonlinearities, contrast normalisation, spatial uncertainty, perceptual learning.

---

## IV. Developments within the linear model

| Topic | Key point |
|---|---|
| **Optimal weighted sums** | For an **unbiased, stable** observer, the standard method is the **most efficient weighted sum**, whatever the level of internal noise. It beats the simple correlation method. Better weightings exist when the observer is **biased**, gives **ratings**, or sees **multiple contrast levels**. |
| **GLM** (Knoblauch & Maloney, 2008) | A linear observer *is* a generalised linear model: **probit regression** of responses on the stimulus. The coefficients are **w / σ_z**, i.e. the template in units of internal noise. **Advantages:** established statistical tools; priors are easy to add; **no assumption about the stimulus distribution** (stimuli need not be Gaussian noise; Abbey & Eckstein (2001) used natural images). In simulations with realistic internal noise, the GLM and the weighted sum performed about equally. **Caveat:** GLM maximum-likelihood estimates can be biased. |
| **Statistical tests** | **Hotelling T²** to compare a CI with a hypothesised template or with another CI. **Double-pass consistency** (Burgess & Colborne, 1988) to estimate internal noise. **Random field theory** for pixel-wise tests with multiple comparisons. **Neri & Levi (2006):** validate a CI by using it as a simulated observer's template and checking how well it predicts the human's **trial-by-trial responses**. |
| **Dimensionality reduction** | Several thousand trials are usually needed because the model has far more freedom than it needs (a pixel-by-pixel checkerboard template is allowed but implausible). **Remedies:** fewer pixels; noise along a few task-relevant dimensions; **priors** (shrinkage, smoothness, sparsity, e.g. a Gaussian pyramid in Mineault et al., 2009). Choose a prior whose bias is **irrelevant to the hypothesis being tested**. |
| **Multiple response alternatives** | **Multinomial logistic regression (softmax)** gives *m*-alternative CIs. Murray shows that a simpler correlation method (Dai & Micheyl, 2010) **does not converge**: each CI becomes a **mixture of all the templates**. |
| **Bubbles** (multiplicative noise; Gosselin & Schyns, 2001) | For a linear observer it recovers **less** than a CI does. More promising for studying **nonlinear** strategies. |

---

## V. Beyond the linear observer

- **Late monotonic nonlinearity is harmless** (Birdsall's theorem): the standard CI still recovers w.
- **Nonlinear features:** apply the same methods to transformed inputs (e.g. energy, squared contrast). Neri & Heeger (2002) used both first-order and variance-based CIs.
- **Second-order kernels:** decision variable of the form

  ```
  d = wᵀx + xᵀWx + z
  ```

  **W** can be estimated from the **covariance of the noise** in each trial group (Neri, 2004).
- **Uncertainty:**
  - A **phase-uncertain observer** uses an energy rule.
    - On **signal-absent** trials its CI is **empty**, even though the observer clearly has a strategy.
    - On **signal-present** trials its CI **matches whatever signal is shown**, even a template that exists nowhere in the observer.
  - **Lesson:** for nonlinear observers, **the CI depends on the interaction between signal, noise and strategy.**
  - Tjan & Nandy (2006) used the differences between CIs to measure spatial uncertainty.
  - **PCA of the noise in each response group** can reveal several decision-relevant directions. Use with caution.
- **Other fields:** neurophysiology (m-sequences, natural images, receptive-field mapping) and statistical learning have tools psychophysics could borrow.

---

## VI. What CIs have found

| Area | Findings |
|---|---|
| **Spatial vision** | Observers **change their templates** depending on the noise spectrum and noise amplitude. **Noise is not strategy-neutral.** |
| **Perceptual organisation** | Observers give weight along **illusory and occluded contours** that carry no physical signal. Strong individual differences. |
| **Visual search** | Templates for saccades match the target shape, with limits. About **100 ms "dead time"** before the first saccade. |

---

## VII. Conclusions

- **Linearity has been less limiting than feared.** It can be tested, and it often holds over the small stimulus range of a typical experiment.
- **Don't overclaim.** CIs do not simply reveal the decision rule:
  - different rules can produce the **same** CI;
  - one rule can produce **different** CIs with different stimuli.
  - CIs have mostly added **converging evidence**, rather than findings impossible to get any other way.
- **A CI is a behavioural measure** (like a threshold), not full system identification.
- **Future direction:** likelihood-based, **probabilistic (including nonlinear) observer models fitted to responses**. Difficulties Murray names: local maxima, identifiability, estimating bias and variance, robustness when model assumptions are violated.

---

## Key terms

| Term | Meaning |
|---|---|
| **Template (w)** | The weight vector the linear observer applies to the stimulus |
| **Internal noise (z, σ_z)** | Trial-to-trial randomness inside the observer, independent of stimulus noise |
| **Decision variable (d)** | wᵀ(signal + noise) + z, compared with a criterion |
| **Standard CI** | Ahumada's sum/difference of mean noise across the four signal × response groups |
| **Wiener / Volterra kernels** | Series expansion of a system's response; the first-order kernel ≈ a CI |
| **GLM / probit CI** | Template estimated by regressing responses on stimuli; coefficients = w / σ_z |
| **Double-pass** | Showing the same stimuli twice to estimate internal noise from response consistency |
| **Birdsall's theorem** | A late monotonic nonlinearity does not change what the CI recovers |
| **Second-order kernel (W)** | Pairwise term xᵀWx; estimated from noise covariances |
| **Bubbles** | Multiplicative-noise method (Gosselin & Schyns) that reveals regions used by the observer |

---

## Relevance to this project (RC with a simulated person)

| Point in Murray | Implication for the project |
|---|---|
| **GLM makes no assumption about the stimulus distribution** | The key support for **estimator B**. Simple averaging needs random noise; a likelihood model of the choices does not, so it can in principle be fitted to **adaptive IEC stimuli**. |
| **Multinomial logistic = softmax** | The same as the project's choice model **P(x \| S) ∝ exp(β·u(x))**, with S the set of images shown. |
| **Naive averaging in m-alternative tasks is biased** (Dai & Micheyl case) | A warning for IEC: **averaging the selected k-of-n images is not safe**, even before adaptivity is considered. |
| **Coefficients = w / σ_z** | Template strength and internal noise **cannot be separated**, so claim recovery of the **direction** only. |
| **Thousands of trials; priors and low dimensions help** | Supports working in a **low-dimensional StyleGAN/PCA latent space**, and possibly a smoothness or shrinkage prior on **θ**. |
| **Neri & Levi prediction test** | Matches the project's **predictive accuracy on held-out choices** metric. |
| **Double-pass internal noise** | A way to measure **σ in Study 2**, and to bound how reliable any template estimate can be. |
| **Nonlinear observers: the CI depends on where you sample** | This is the **ideal-point problem**. An ideal-point preference **u = −‖x − θ‖²** is a **second-order observer**. Around a base **b**, its first-order CI points toward **θ − b**, so the result depends on the base. That is where a **moving IEC base** is both a risk and an opportunity. |
| **Future direction: probabilistic models fitted to responses** | The project is a version of this future direction, **extended to adaptive stimuli**. |
