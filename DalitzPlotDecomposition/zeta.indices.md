# Helicity bookkeeping for $1\to3$ decays

We describe a decay of one initial particle $0$ into three final particles $1,2,3$.
Spins are $j_0, j_1, j_2, j_3$.
Helicity labels $\lambda_0, \lambda_1, \lambda_2, \lambda_3$ are **not invariant**; their definition depends on the chosen reference frame.

A caviet: when considering the system rest frame, particle 0 is at rest, helicity is undefined.
In thins case, $\lambda_0$ referes to $m_0$, the spin projection to $z$-axis, on the canonical basis.

---

## Frames and geometry

Three canonical final-state frames are defined by which pair of particles is at rest:

* **Frame (1)**: rest of pair $(2,3)$.
* **Frame (2)**: rest of pair $(3,1)$.
* **Frame (3)**: rest of pair $(1,2)$.

Additionally, the **initial rest frame** (particle 0 at rest) is often used for production processes.

The geometry in these frames is fixed by convention: all momenta lie in a single plane, the decay plane is taken as $xz$, and the $y$-axis is normal. The $z$-axis is chosen along a convenient particle momentum (commonly the bachelor in each pair frame).

Example sketch for **Frame (1)** (rest of pair (23)):

```
             x p2
            /
 p0, p1    / ↗ λ2
<---------o
   ←     /
  -m0   /  ↙ λ3
       x p3
```

Here particle 0 decays, particle 1 recoils to the left, while particles 2 and 3 are back-to-back, carrying helicities $\lambda_2,\lambda_3$.

---

## Helicity definitions

There is no strict requirement that all helicities be defined in the same frame.
In practice, each particle’s helicity is most naturally defined in the two-body subsystem that contains it:

* $\lambda_1^{[3]}$ in frame (3), the rest of pair (12).
* $\lambda_2^{[1]}$ in frame (1), the rest of pair (23).
* $\lambda_3^{[2]}$ in frame (2), the rest of pair (31).

This convention ensures that each helicity is the projection of the particle’s spin along its momentum in the corresponding two-body rest frame.

If one wishes to bring helicities into a common frame, this is possible but requires Wigner rotations.

---

## Wigner rotations

Switching the helicity definition of particle $i$ between frames involves a Wigner rotation.
We denote the angle by

$$
\zeta^{\,i}_{f(g)} ,
$$

where

* $i$: particle index,
* $f$: current frame,
* $(g)$: reference frame.

This notation makes the direction of the transformation explicit: from $g$ to $f$.

### Transformation of states

$$
\big|\,i,\lambda_i^{[g]}\big\rangle
= \sum_{\lambda_i'} D^{\,j_i}_{\lambda_i'\lambda_i}\!\left(\zeta^{\,i}_{f(g)}\right)\,
\big|\,i,\lambda_i'^{[f]}\big\rangle .
$$

* $D^{\,j}_{\lambda'\lambda}$ is the Wigner $D$-matrix (often reducing to a little-$d$ in planar cases).
* For **final-state particles** in an amplitude, these matrices enter as $D^*$, since amplitudes involve $\langle \text{final}|$.
* The order of indices matters: $\lambda$ is the helicity label in the **reference** frame, $\lambda'$ in the **current** frame.

Because the rotations are unitary, the squared amplitude summed over helicities is invariant under the choice of frames. This is why mixed-frame helicity definitions are widely used.

---

## Compact reference block

* Frames: $(1)\equiv(23)$ rest, $(2)\equiv(31)$ rest, $(3)\equiv(12)$ rest.
* Helicity notation: $\lambda_i^{[f]}$ = helicity of particle $i$ in frame $f$.
* Wigner angle: $\zeta^{\,i}_{f(g)}$ = rotation for particle $i$ from frame $g$ to $f$.
* State relation:

  $$
  |\,i,\lambda^{[g]}\rangle = \sum_{\lambda'} D^{\,j_i}_{\lambda'\lambda}(\zeta^{\,i}_{f(g)}) |\,i,\lambda'^{[f]}\rangle .
  $$
* Invariance: $\sum_{\{\lambda\}} |A(\{\lambda^{[f]}\})|^2$ is independent of the chosen frames.

---

## Common pitfalls (and how to avoid them)


1. **Direction of the transformation.**
   Always check whether you are rotating from frame $g$ to $f$ or the other way. Swapping these changes whether the $D$-matrix should be complex conjugated.

2. **Misreading the frame indices.**
   Frame (1) refers to the **(23) rest frame**, not “the frame of particle 1.”

6. **Identical particles.**
   For the case of identical particle in the final state, the particle labels distinguish them.
   Helicity amplitudes is not necessarily symmetric under the permutation of indices due to helicity quantization access.
   The observed quantities, e.g. unpolarized decay rate, althogh, must be symmetric under the swap of indices once the amplitude is symmetrized.

3. **Assuming all helicities must share a frame in which they are defined.**

   * They don’t. Mixed-frame helicities are fine; unpolarized observables are invariant due to unitarity.

5. **Conventions for $D^J$.**

   * We use Wikipedia conventions == Martin Spearman's book conventions, it's different to Mathematica conventions.

6. **Confusing helicity angles with Wigner angles.**

   * “Helicity angle” (e.g., the polar angle of a decay product) is a kinematic angle; $\zeta$ is the **rotation of spin quantization axes between frames**.

7.  **Invisible dependence in code.**

    * The quantization axis dependence is often omitted in the presentation, code. It leads to confusion.
    Omittion of the Wigner rotations makes the decay amplitude incorrect, namely it affects interference between different topologies.
    It would have been clearer to make the current/reference order explicit, but clearly it makes notations more cumbersome.

---

## Compact reference block (drop-in)

* Frames: $1\equiv(23)$ rest, $2\equiv(31)$ rest, $3\equiv(12)$ rest.
* Amplitude: $A(\lambda_0^{[f_0]},\lambda_1^{[f_1]},\lambda_2^{[f_2]},\lambda_3^{[f_3]})$.
* Wigner angle: $\zeta^{\,i}_{f}(g)$ = rotate particle $i$ **from** $g$ **to** $f$.
* Transform: $|i,\lambda^{[f]}\rangle=\sum_{\lambda'} D^{J_i}_{\lambda\lambda'}(\zeta^{\,i}_{f}(g))\,|i,\lambda'^{[g]}\rangle$.
* Unpolarized invariance: unitary $D\Rightarrow \sum_{\{\lambda\}}|A|^2$ independent of $[f_i]$.


## Example

```
## Setup: reference topology: 1

A_{lam1,lam2,lam3} = A(k=3)_{lam1',lam2',lam3'}
    * d_{lam1',lam1}(zeta^1_{3(1)})
    * d_{lam2',lam2}(zeta^2_{3(1)})
    * d_{lam3',lam3}(zeta^3_{3(1)})

```