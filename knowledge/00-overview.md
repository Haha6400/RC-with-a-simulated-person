1.  **In a 2IFC RC trial, what exactly does the person see, and what do they choose?**
    

Trong một thí nghiệm phân loại phản hồi (RC) dạng 2IFC (lựa chọn cưỡng bức hai khoảng thời gian), người tham gia thực sự nhìn thấy gì và họ đưa ra lựa chọn như thế nào?

Ans: They see two images side by side: base + n and base − n. The base is a neutral or average face. n is a freshly drawn random noise pattern (white, sinusoid or Gabor). Which image appears on the left is randomised.

They choose the image that better fits the concept, e.g. "Which face looks more trustworthy?" The choice is forced: they must pick one even if both look the same.

The trial is repeated with a new n each time, typically 300–1000 times. Because the base is identical in both images, the choice is really a judgement about one question: "Does this noise pattern push the face toward my concept, or away from it?"

1.  **How is the classification image computed from the trials?**
    

Hình ảnh phân loại (classification image) được tính toán từ các lượt thử nghiệm như thế nào?

Record the noise in the chosen image on each trial: n\_t if they picked base + n, or −n\_t if they picked base − n. Then average:

CI = (1/N) Σ s\_t · n\_t, with s\_t = +1 or −1.

Features that matter are chosen consistently in the same direction, so they accumulate.

Features that do not matter come out + on some trials and − on others, so they cancel.

For display, the raw CI is very small, so it is scaled and overlaid on the base: base + k·CI. Scaling can be independent per person or dependent across people. It does not change the direction of the CI, so cosine similarity in simulation is unaffected.

4AFC: a weighted average, with weights set by the response category.

Group CI: average the unscaled noise across people, then scale. This assumes everyone shares one template.

1.  **What is a "linear observer", and why does RC need that assumption?**
    

"Người quan sát tuyến tính" (linear observer) là gì và tại sao phương pháp RC lại cần giả định này?

**Definition.**  linear observer scores each stimulus as a weighted sum of its features, which is the dot product with an internal template, adds internal noise, and compares the result with a criterion. RC needs this assumption because it guarantees that the average of the chosen noise points in the direction of the template. Without it, the classification image is no longer a clean picture of the template.

The model (Murray's notation): 

d = wᵀ(s\_k + n) + z, respond "2" if d > a, otherwise "1"

*   w is the template, s\_k is the signal shown, n is the external stimulus noise, z is internal noise, and a is the criterion (response bias).
    
*   If the observer has one template per response (t₁, t₂), only the difference template w = t₂ − t₁ affects choices. With m alternatives, only template differences can be recovered. In a 2IFC face task, this means you recover "trustworthy minus untrustworthy" relative to the base, not an absolute picture.
    

**Why RC needs it.** 

*   Why the CI works for this observer (the geometric argument):
    
    *   The decision boundary wᵀg = a is a hyperplane perpendicular to w.
        
    *   Gaussian noise is spherically symmetric, so the average noise on each side of the boundary is shifted only along w.
        
    *   Therefore the averaged noise, combined with the right signs, gives E\[CI\] ∝ w, an unbiased estimate of the template.
        
    *   This still holds with internal noise, as long as the internal noise is independent of the stimulus noise.
        
    *   For a linear observer, the experimenter's choice of signal or base does not change what the CI estimates.
        

*   What the assumption does not forbid:
    
    *   A late monotonic nonlinearity, d\* = f(wᵀg + z), is harmless. It is equivalent to a linear observer with a transformed criterion (Birdsall's theorem).
        
    *   The GLM view: the linear observer is a probit regression of responses on the stimulus. Its coefficients are w/σ\_z, so you recover the template's direction, but its strength cannot be separated from internal noise.
        

*   What happens when the observer is not linear:
    
    *   The CI becomes a local, first-order summary of the strategy, and it can depend on the stimuli and the base, not only on the observer.
        
    *   Ideal-point example: the observer prefers a moderate smile. If the base already has that smile, adding or removing smile both look worse. The CI shows nothing for smile, even though the observer cares about it a great deal.
        
    *   Phase-uncertain (energy) observer: the CI on signal-absent trials is empty, although the observer has a clear strategy. On signal-present trials it reproduces whatever signal was shown, even a template that exists nowhere in the observer.
        
    *   Remedies include second-order kernels (covariance of noise per response class; Neri, 2004), nonlinear transforms of the stimulus (e.g. energy), PCA, and explicit nonlinear models fitted by likelihood.
        

Murray's verdict: linearity is a simplification. It ignores spatial uncertainty, contrast normalisation and learning. But it can be tested (e.g. by comparing CIs across trial subsets), it often holds over the small stimulus range of an experiment, and different decision rules can still produce the same CI. Treat the CI as a behavioural measure, not as a complete readout of the decision rule.

1.  **What is internal noise, and how does it affect the number of trials needed?**
    

Nhiễu nội tại (internal noise) là gì và nó ảnh hưởng thế nào đến số lượng lượt thử nghiệm cần thiết?

Internal noise is trial-to-trial randomness inside the observer, independent of the stimulus. It means the same stimulus does not always produce the same response. It does not bias the CI, but it makes each response less informative, so more trials are needed for the same precision.

**Definition and sources:**

*   In the model it is the term z in d = wᵀg + z, with variance σ\_z², and independent of the external noise n.
    
*   Sources: neural noise, lapses of attention, fluctuations in memory or the criterion, fatigue, motor errors.
    
*   It is usually expressed as the internal-to-external noise ratio. Murray cites realistic values of about 0.5 or higher for human observers (Neri, 2010a).
    

**Effect on the CI:**

*   No bias. The geometric argument still holds, so E\[CI\] ∝ w. The standard weighted-sum method stays the optimal weighted sum whatever the level of internal noise (Abbey & Eckstein, 2002b; Ahumada, 2002; Murray et al., 2002).
    
*   Lower signal-to-noise ratio. More responses are driven by z instead of by n, so the stimulus-related signal in the CI grows more slowly relative to sampling noise.
    
*   Smaller GLM coefficients, which scale as w/σ\_z. With realistic internal noise, the GLM, GAM and weighted-sum estimates end up with about the same quality (Knoblauch & Maloney, 2008).
    
*   A ceiling on predictability. No template can predict a noisy observer's trial-by-trial choices perfectly. The ceiling depends on σ\_z, which is why Neri & Levi's (2006) validation test needs an estimate of internal noise.
    

**Effect on the number of trials** (from our Stage 0 simulation, where the approximation matched simulated observers to within about 0.02):

cos²(CI, w) ≈ N·a² / (N·a² + d), with a² = (2/π)/(1 + σ²)

so trials **needed** ∝ d·(1 + σ²). Here σ is internal noise relative to the stimulus-driven part of the decision variable, d is the number of dimensions, and N is the number of trials.

Raising σ from 1 to 2 multiplies the required trials by 2.5. This combines with high dimensionality, and together they explain why pixel-based CIs need thousands of trials.

**How it is measured: the double-pass method** (Burgess & Colborne, 1988; Ahumada, 2002):

*   Present the same noisy stimuli twice and measure how often the observer agrees with themselves. High agreement means low internal noise.
    
*   Combined with percent correct, this gives the internal-to-external noise ratio.
    
*   Ahumada (2002) uses it to test a hypothesised template. Compute the internal noise needed to explain performance if the observer used that template. If this disagrees with the double-pass estimate, reject the template.
    

**Why it matters for your project:**

*   It limits test–retest reliability for both RC and IEC.
    
*   It is the noise term in your simulated observer (σ in experiment E2).
    
*   Because only w/σ\_z is identifiable, your research question should claim recovery of the template's direction, not its absolute strength.
    

1.  **How do you test whether a classification image contains real information rather than pure noise (the idea behind infoVal)?**
    

Làm thế nào để kiểm tra xem một hình ảnh phân loại có chứa thông tin thực sự hay chỉ toàn là nhiễu (ý tưởng đằng sau infoVal)?

Idea: ask whether a person answering at random would produce a CI this strong.

Keep the exact same stimuli (the same noise patterns).

Simulate many random responders, each choosing + or − at random, and compute their CIs. This gives the null distribution.

Measure the strength (norm) of each CI. Random responses mostly cancel, so null CIs are weak. Consistent responses accumulate, so real CIs are strong.

Express the observed CI's norm as a z-score against the null. As far as I remember, Brinkman et al. (2019) use a robust version based on the median and MAD; check the paper for the exact formula.

infoVal > 1.96: detectable signal. infoVal < 1.96: treat the CI as noise.

Small demo (d = 50, 300 trials, σ = 1, 1,000 random responders): the observed CI norm was 0.652, the null median was 0.41, and the infoVal-like z was 6.2. That is clearly informative.

What infoVal does not tell you: whether the signal is the right template. It only shows that the responses are consistent rather than random. A person with a consistent but idiosyncratic criterion still scores high. Detection also gets easier with more trials. For accuracy you need ground truth (simulation), a reference CI, or predictions of held-out choices.

**Note for the IEC+RC project:** for IEC data the null is harder to build. A random responder in IEC changes which images appear later, so the null must simulate the whole IEC run with random selections, not just reshuffle the choices.