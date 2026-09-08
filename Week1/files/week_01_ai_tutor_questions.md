# Week 1 — AI Tutor Questions (In-Class)

**Module:** ADS203TC — Signal and Image Processing
**Lecture:** Lecture 1 — Introduction to Signal Processing
**How to use:** During the lecture, at the slide indicated, ask the AI tutor the
question, **predict the answer yourself**, then type the tiny code snippet and run it.
The code is the arbiter — if the AI tutor and the code disagree, trust the code and
flag the disagreement.

These questions are written for the LMC AI Tutor (see `ai_tutor/` for its setup and
system prompt). Each takes roughly two minutes.

---

## Part 1 — Signals

### Q1 · Discrete-time vs digital — *slide "The Digital Signal"*

**Ask the AI tutor:**
> What is the difference between a discrete-time signal and a digital signal?

**Verify with code:**
```python
import numpy as np
n = np.arange(0, 10)
x = np.cos(0.5 * n)          # discrete-time, continuous-valued
q = np.round(x * 10) / 10    # amplitude quantised -> digital
print("continuous-valued :", np.round(x, 3))
print("digital (quantised):", np.round(q, 3))
```
**Check:** `x` has many distinct values; `q` collapses to a few repeated levels.
A signal is *digital* only when BOTH time and amplitude are discrete.

---

### Q2 · Signal classification — *slide "Exercise 1.1.1"*

**Ask the AI tutor:**
> Classify x(n)=sin(0.1·π·n) stored as a 16-bit float: continuous or discrete in
> time? in amplitude? deterministic or random?

**Verify with code:**
```python
import numpy as np
n = np.arange(0, 16)
x = np.sin(0.1 * np.pi * n)
print("values:", np.round(x, 4))
print("number of distinct values:", len(np.unique(np.round(x, 4))))
print("deterministic? formula gives identical result every run:", np.allclose(x, np.sin(0.1*np.pi*n)))
```
**Check:** defined on integer `n` (discrete time); 16-bit storage forces finite
precision (discrete amplitude); a closed formula (deterministic).

---

## Part 2 — Sinusoids

### Q3 · Discrete periodicity — *slide "Property 1.2.4: DT Periodicity"*

**Ask the AI tutor:**
> When is a discrete sinusoid cos(ωn) periodic, and how do I find its period N?

**Verify with code:**
```python
import numpy as np
def period_of(f, nmax=1000):
    n = np.arange(0, 50)
    for N in range(1, nmax):
        if np.allclose(np.cos(2*np.pi*f*(n+N)), np.cos(2*np.pi*f*n)):
            return N
    return None

print("f = 3/8        -> period:", period_of(3/8))          # rational -> 8
print("f = 3/(2*pi)   -> period:", period_of(3/(2*np.pi)))   # irrational -> None
```
**Check:** rational `f = k/N` gives period `N`; irrational `f` never repeats.

---

### Q4 · Aliasing — *slide "Property 1.2.5: Aliasing"*

**Ask the AI tutor:**
> Why are the frequencies ω and ω+2π indistinguishable in discrete time? Give me a
> concrete numerical example.

**Verify with code:**
```python
import numpy as np
n = np.arange(0, 20)
w = 0.2 * np.pi
x1 = np.cos(w * n)
x2 = np.cos((w + 2*np.pi) * n)
print("max |x1 - x2| =", np.max(np.abs(x1 - x2)))   # ~0  -> identical sequences
```
**Check:** the two signals are numerically identical, so the sequence cannot tell
them apart. This is why only the range −π ≤ ω ≤ π matters.

---

### Q5 · Energy vs power — *slide "Energy vs. Power Signals"*

**Ask the AI tutor:**
> What distinguishes an energy signal from a power signal? Which one is cos(ωn)?

**Verify with code:**
```python
import numpy as np
n = np.arange(0, 100)
x = 0.5**n
print("energy of 0.5^n      :", np.sum(x**2))         # finite -> energy signal

n = np.arange(0, 1000)
x = np.cos(0.2 * np.pi * n)
print("power of cos(0.2*pi*n):", np.mean(x**2))       # ~0.5 finite -> power signal
```
**Check:** decaying transient has finite energy; the endless sinusoid has finite
average power (≈ 0.5 for unit amplitude) but infinite energy.

---

## Part 2 — Systems

### Q6 · Linearity — *slide "Property 1.2.7: Linearity"*

**Ask the AI tutor:**
> How do I test whether a system is linear? Is y(n)=x(n)² linear?

**Verify with code:**
```python
import numpy as np
n = np.arange(0, 10)
x1, x2 = np.sin(n), np.cos(n)
a, b = 2, 3
T = lambda x: x**2                          # system under test
lhs = T(a*x1 + b*x2)                        # system applied to weighted sum
rhs = a*T(x1) + b*T(x2)                     # weighted sum of outputs
print("superposition holds (linear)?", np.allclose(lhs, rhs))   # False -> non-linear
```
**Check:** change `T = lambda x: x**2` to `T = lambda x: 3*x` and re-run — it becomes
`True`. Superposition is the test.

---

### Q7 · Time-invariance — *slide "Property 1.2.8: Time-Invariance"*

**Ask the AI tutor:**
> How do I check time-invariance? Is y(n)=n·x(n) time-invariant?

**Verify with code:**
```python
import numpy as np
n = np.arange(0, 10)
x = np.sin(n)
T = lambda x, n: n * x                      # system y(n) = n·x(n)

y = T(x, n)
y_shifted = np.zeros_like(y); y_shifted[1:] = y[:-1]           # delay the OUTPUT
x_shifted = np.zeros_like(x); x_shifted[1:] = x[:-1]           # delay the INPUT
print("time-invariant?", np.allclose(y_shifted[1:], T(x_shifted, n)[1:]))  # False
```
**Check:** delaying the output gives `(n-1)·x(n-1)`, but the system on a delayed
input gives `n·x(n-1)`. They differ, so the system is time-variant.

---

### Q8 · Causality — *slide "Property 1.2.9: Causality"*

**Ask the AI tutor:**
> Why is y(n)=x(n+1) non-causal, while the 3-point moving average is causal?

**Verify with code:**
```python
import numpy as np
x = np.array([1., 2., 3., 4., 5.])

def causal_ma(x):                       # y(n) = (x(n)+x(n-1)+x(n-2))/3
    y = np.zeros_like(x)
    for n in range(2, len(x)):          # starts at n=2, only past samples
        y[n] = (x[n] + x[n-1] + x[n-2]) / 3
    return y

def acausal_ma(x):                      # needs x(n+1): a future sample
    y = np.zeros_like(x)
    for n in range(1, len(x)-1):
        y[n] = (x[n+1] + x[n] + x[n-1]) / 3
    return y

print("causal (past only)   :", np.round(causal_ma(x), 2))
print("non-causal (uses n+1) :", np.round(acausal_ma(x), 2))
```
**Check:** the causal filter only needs `x(n), x(n-1), x(n-2)` — available now. The
non-causal one must wait for `x(n+1)` — the future. Real-time systems must be causal.

---

## Ground rules (remind students once at the start)

1. **Predict first.** Write down what you expect before running the code.
2. **The code is the referee.** The AI tutor can be confidently wrong — the printed
   output settles it.
3. **Flag disagreements.** If the tutor and the code disagree, that is a finding, not
   a nuisance: report it.
4. **You must check every answer yourself** — the AI tutor is a study aid, not an
   authority.
