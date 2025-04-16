# Numerical Optimization
This repository contains the final project for the course **SI152 - Numerical Optimization** at ShanghaiTech University.  
Our team focused on **Quadratic Programming (QP)** algorithms and implemented custom solvers based on the academic literature.

## Literature Review

We investigated and compared several major classes of QP solvers:

### 1. **Active-Set Methods**  
- Solver: `qpOASES`  
- Fast for small to medium convex QPs  
- Best for real-time MPC problems due to warm-start capabilities  

### 2. **Interior-Point Methods**  
- Solvers: `hpipm`, `OOQP`, `CVXGEN`  
- High accuracy for medium to large-scale QPs  
- Suitable for embedded and control applications  

### 3. **First-order Splitting Methods (ADMM)**  
- Solver: `OSQP`  
- Excellent for large-scale or sparse QPs  
- Provides embedded code generation capabilities  
- Good balance between speed and robustness

## Implementation: IRWA & ADAL

We implemented and tested the following algorithms based on the paper:

> **Burke et al., 2015 – "Iterative Reweighted Linear Least Squares for Exact Penalty Subproblems" (SIAM J. Optim.)**

### Algorithms:
- **IRWA** (Iterative Reweighted Algorithm)
- **ADAL** (Alternating Direction Augmented Lagrangian)

They solve subproblems via matrix-free convex quadratic minimization.

### 1. IRWA – Iteratively Reweighted Algorithm

The IRWA algorithm solves a smoothed approximation to the penalized QP:

$$
\min_{x} \quad \frac{1}{2} x^T H x + g^T x + \sum_i \sqrt{(A_i x + b_i)^2 + \epsilon_i^2}
$$

- Updates the weight vector \( w_i^{(k)} = \frac{1}{\sqrt{(r_i^{(k)})^2 + \epsilon_i^2}} \)
- Inner loop solves a **weighted least-squares QP**
- Outer loop updates \( \epsilon \) to reduce smoothing over time

### 2. ADAL – Alternating Direction Augmented Lagrangian

We also implement ADAL for the problem:

$$
\min_{x, z} \quad \frac{1}{2} x^T H x + g^T x + \delta(z) \\
\text{s.t.} \quad A x + b = z
$$

- Splits primal variable \( x \) from constraints via auxiliary variable \( z \)
- Uses Lagrangian dual updates and penalty terms to iteratively converge
- Inner loop solves convex QP with augmented terms

## Experimental Setup

We evaluated solver performance from the following perspectives:

- **Parameter sensitivity**: impact of initial relaxation vector ε₀ and update rules
- **Convergence tolerance**: analysis of stopping thresholds
- **Problem size**: scalability with increasing dimensionality
- **Infeasibility**: test behavior under constraint violation

All implementations are in **Python** using **NumPy**, and follow the specification:
- `A1`, `b1`, `A2`, `b2` = equality and inequality constraints
- `H`, `g` = quadratic and linear terms
- Output: optimized solution `x`

## Results Summary

| Metric | IRWA | ADAL |
|--------|------|------|
| Convergence Speed | Faster (most cases) | Slower |
| Stability | High | Moderate |
| Infeasible Case Handling | Better | Acceptable |
| Final Objective Value | Lower (better) | Higher (worse) |

- IRWA often **outperforms ADAL**, especially in high-dimensional or infeasible QP instances.
- ADAL remains robust, but requires careful tuning for convergence.
