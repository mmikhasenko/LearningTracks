# Cascade-Decay Angle Algorithm (Helicity Convention)

## Scope

Compute helicity angles for an exclusive cascade decay from only:

* final-state four-vectors in some global frame (lab or CM), and
* a chosen **binary decay topology** (ordered tree).

The output is the set of $(\theta,\phi)$ per internal node, consistent with the helicity formalism.

---

## Cornerstones

### 1) Multiple valid topologies

* The same exclusive process admits **multiple binary topologies** (different bracketings).
* Each topology yields a **different angle set**, but these parameterize the **same degrees of freedom**.
* Because final particles carry spin, different topology choices imply **different spin quantization axes**. Consistency requires **Wigner rotations** when relating amplitudes across topologies.
* Practical approach: define a **reference topology** and rotate all other topologies into that convention if you compare or combine them.

### 2) Do **not** use generic boosts (canonical convention)

* Generic “boost a 4-vector by β” routines (e.g. ROOT `TLorentzVector::Boost`) effectively do
  **rotation → pure boost along z → rotation back**, i.e. the **canonical** convention.
* **Helicity convention used here:** perform needed **alignment rotation(s)** and then a **pure boost along z**, with **no rotation back**.
* This ensures the helicity axis always follows the daughter’s momentum.

---

## Conventions and primitives

* Metric $(+,-,-,-)$. Active transforms; matrices act on column 4-vectors, composed right-to-left.
* Rotations: $R_z(\phi)$, $R_y(\theta)$.
* Pure boost along $+\hat z$:

  $$
  B_z(\beta)=
  \begin{pmatrix}
  \gamma & 0 & 0 & \gamma\beta\\
  0 & 1 & 0 & 0\\
  0 & 0 & 1 & 0\\
  \gamma\beta & 0 & 0 & \gamma
  \end{pmatrix},\quad \gamma=(1-\beta^2)^{-1/2}.
  $$
* Spherical angles of nonzero $\vec v$:
  $\phi=\operatorname{atan2}(v_y,v_x),\ \theta=\arccos(v_z/\|\vec v\|)$.

---

## Inputs

1. Final four-vectors $\{p_i^{\text{global}}\}$ of all final particles.
2. A **binary topology**. It fixes a **unique path** from the root to any particle and thus fixes the **sequence of frames** visited.

**Global frame policy** (must be stated):

* Use the lab frame as given, or
* Pre-boost to the total CM and, optionally, align $+\hat z$ (e.g. to beam/parent).

Different choices change numerical angles but not the algorithm.

---

## Per-node algorithm (parent $A\to 1+2$)

Assume you arrive at node $A$ with cumulative transform $T_A$. By construction, $T_A$ already brings $A$ into its rest frame.

1. **Apply $T_A$ to daughters**

   $$
   P_1 = T_A p_1,\quad P_2 = T_A p_2,\quad P_A = P_1+P_2=(m_A,\vec 0).
   $$

2. **Angles from daughter 1**

   $$
   \phi_1=\operatorname{atan2}((P_1)_y,(P_1)_x),\quad
   \theta_1=\arccos\!\big((P_1)_z/\|\vec P_1\|\big).
   $$

   Store $(\theta_1,\phi_1)$ as the helicity angles of this node.

3. **Child transforms**

   * Child 1:

     $$
     L_{A\to 1} = B_z(-\beta_1)\, R_y(-\theta_1)\, R_z(-\phi_1),
     \quad \beta_1=\|\vec P_1\|/E_1.
     $$
   * Child 2 (Jacob–Wick):

     $$
     L_{A\to 2} = B_z(-\beta_2)\, R_y(\pi)\, R_y(-\theta_1)\, R_z(-\phi_1),
     \quad \beta_2=\|\vec P_2\|/E_2.
     $$

4. **Recurse**

   * For child 1: $T_1 = L_{A\to 1}\,T_A$.
   * For child 2: $T_2 = L_{A\to 2}\,T_A$.

---

## Global traversal

Depth-first over the binary tree:

1. Set $T_{\text{root}}$ per your global frame policy.
2. At each node: apply $T_A$ to daughters, extract $(\theta,\phi)$, build $L_{A\to 1}$ and $L_{A\to 2}$, recurse.

Output: one pair $(\theta,\phi)$ per internal node.

---

## Spin and topology alignment

* Angle sets from different topologies are **not independent**; they re-encode the same dof.
* Changing topology changes helicity axes; to compare/combine amplitudes you must apply the **corresponding Wigner rotations**.
* Define a **reference topology** and align others to it for consistency.

---

## Numerical hygiene / edge cases

* **Soft/threshold:** if $\|\vec P_1\|<\varepsilon$, set $(\theta,\phi)=(0,0)$.
* **Massless daughter:** angles fine at the node; but no rest frame → stop recursion for that branch.
* **Clamp** $\cos\theta\in[-1,1]$ before $\arccos$.
* **Labeling:** keep “daughter 1” fixed; affects angle definitions and Jacob–Wick step.

---

## Minimal pseudocode (helicity-safe)

```python
def process_node(A, T_A):
    # Apply cumulative transform: A is already at rest
    p1, p2 = daughters(A)
    P1, P2 = apply(T_A, p1), apply(T_A, p2)
    PA = P1 + P2  # should be (m_A, 0,0,0)

    # Angles from daughter 1
    theta1, phi1 = spherical(P1.spatial)
    store_angles(A, theta1, phi1)

    # Child-1 transform
    L1 = boost_z(-beta(P1)) @ Ry(-theta1) @ Rz(-phi1)

    # Child-2 transform (Jacob–Wick)
    L2 = boost_z(-beta(P2)) @ Ry(pi) @ Ry(-theta1) @ Rz(-phi1)

    # Recurse
    if decays(child1(A)):
        process_node(child1(A), L1 @ T_A)
    if decays(child2(A)):
        process_node(child2(A), L2 @ T_A)
```

---

## Validation checks

* In $A$ rest: $\vec P_1+\vec P_2=\vec 0$.
* After $R_z(-\phi_1)R_y(-\theta_1)$: $\vec P_1 \parallel +\hat z$.
* After $B_z(-\beta_1)$: daughter 1 at rest.
* After Jacob–Wick flip: daughter 2 aligned to $+\hat z$ before boost.

---

## Implementation note

Avoid framework routines that hide rotate–boost–rotate-back (canonical). Always implement explicit **alignment rotation(s) + pure $B_z$** sequence and propagate transforms down the tree.
