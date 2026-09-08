# Lecture 1: Introduction to Signal Processing — Quiz Questions

## Part 1

### Q1. Which of the following correctly defines a digital signal?

- A) A signal that is continuous in time but discrete in amplitude.
- B) A signal that is discrete in time but continuous in amplitude.
- C) A signal that is discrete in both time and amplitude.
- D) A signal processed by a digital computer, regardless of its time or amplitude representation.

**Answer:** C — A strictly digital signal is discrete in both time (sampled at specific instants) and amplitude (quantized to a finite set of levels).

### Q2. A system is defined by $y[n] = x[n] + 5$. Is this system linear?

- A) Yes, because it is a sum of terms involving $x[n]$.
- B) Yes, because the output scales proportionally with the input.
- C) No, because it fails the additivity property of superposition.
- D) No, because adding a constant violates the scaling (homogeneity) property of superposition.

**Answer:** D — For a linear system, $\mathcal{T}[a x[n]] = a \mathcal{T}[x[n]]$ must hold. Here, $\mathcal{T}[a x[n]] = a x[n] + 5$, but $a \mathcal{T}[x[n]] = a(x[n] + 5) = a x[n] + 5a$. These are only equal when $a=1$, so the system is nonlinear (specifically, it is an affine system with a non-zero bias term).

## Part 2

### Q3. A discrete-time sinusoid $x[n] = \cos(3n)$ is:

- A) Periodic with fundamental period $N = 3$.
- B) Periodic with fundamental period $N = 2\pi/3$.
- C) Aperiodic because its digital frequency is irrational.
- D) Aperiodic because the discrete time index $n$ is an integer.

**Answer:** C — The digital frequency is $f = \omega/(2\pi) = 3/(2\pi)$, which is irrational (since $\pi$ is irrational). A discrete sinusoid is periodic only if $f$ is a rational number $f = k/N$.

### Q4. A signal $x[n] = (0.5)^n u[n]$ is classified as:

- A) A power signal, because it has infinite duration.
- B) An energy signal, because its total energy $E$ is finite.
- C) Both an energy and a power signal.
- D) Neither an energy nor a power signal.

**Answer:** B — The total energy is $E = \sum_{n=0}^{\infty} (0.25)^n = 1/(1-0.25) = 4/3 < \infty$, which is finite. Consequently, the average power $P = 0$. This is an energy signal with a decaying transient.
