
---

# Comparative Overview of Non-Gaussian Distributions Across Platforms

---

## 1. Gamma Distribution

| Platform                     | Function / Class          | Parameters                      | Parameterization                         | Notes                                                         |
| ---------------------------- | ------------------------- | ------------------------------- | ---------------------------------------- | ------------------------------------------------------------- |
| SciPy (Python)               | `scipy.stats.gamma`       | `a` (shape), `loc`, `scale`     | Shape–scale: `Gamma(a, scale)`           | `scale` is the inverse of rate; mean = `a × scale`            |
| TensorFlow Probability (TFP) | `tfp.distributions.Gamma` | `concentration` (shape), `rate` | Shape–rate: `Gamma(concentration, rate)` | `rate` is the inverse of scale; mean = `concentration / rate` |
| R                            | `stats::rgamma`           | `shape`, `rate`, `scale`        | Shape–rate or Shape–scale                | `scale` is the inverse of rate; mean = `shape × scale`        |
| Julia (Distributions.jl)     | `Distributions.Gamma`     | `α` (shape), `θ` (scale)        | Shape–scale: `Gamma(α, θ)`               | `θ` is the scale parameter; mean = `α × θ`                    |

**Key Observations:**

* **Parameter Naming Conventions:** Different libraries use varying names (`a`, `concentration`, `shape`, `α`) for the shape parameter.
* **Inverse Relationships:** `scale` vs `rate` flips depending on the library.
* **Mean Calculation:** Mean formula differs based on whether rate or scale is used.

**Potential Pitfalls:**

* **Misinterpretation of Parameters:** Confusing `rate` and `scale` leads to incorrect results.
* **Inconsistent Parameterization:** Cross-library scripts must standardize parameter usage.

---

## 2. Beta Distribution

| Platform                     | Function / Class         | Parameters                         | Parameterization                                    | Notes                                                       |
| ---------------------------- | ------------------------ | ---------------------------------- | --------------------------------------------------- | ----------------------------------------------------------- |
| SciPy (Python)               | `scipy.stats.beta`       | `a`, `b`, `loc`, `scale`           | Shape–scale: `Beta(a, b, loc, scale)`               | `loc` and `scale` adjust support; default support is \[0,1] |
| TensorFlow Probability (TFP) | `tfp.distributions.Beta` | `concentration1`, `concentration0` | Shape–scale: `Beta(concentration1, concentration0)` | No `loc` or `scale`; support is \[0,1]                      |
| R                            | `stats::rbeta`           | `n`, `shape1`, `shape2`            | Shape–scale: `Beta(shape1, shape2)`                 | Support is \[0,1]; no shift or scaling                      |
| Julia (Distributions.jl)     | `Distributions.Beta`     | `α` (shape), `β` (shape)           | Shape–scale: `Beta(α, β)`                           | Support is \[0,1]; no shift or scaling                      |

**Key Observations:**

* **Parameter Naming Conventions:** Naming differs (`a,b` vs `shape1, shape2` vs `α, β`).
* **Support Transformation:** Only SciPy allows `loc` and `scale` to shift/scale the domain.

**Potential Pitfalls:**

* **Misunderstanding Support:** Assuming support is always \[0,1] can cause mistakes when using SciPy’s `loc`, `scale`.
* **Inconsistent Parameterization:** Results mismatch if parameters aren’t standardized.

---

## 3. Dirichlet Distribution

| Platform                     | Function / Class              | Parameters      | Parameterization                          | Notes                                       |
| ---------------------------- | ----------------------------- | --------------- | ----------------------------------------- | ------------------------------------------- |
| SciPy (Python)               | `scipy.stats.dirichlet`       | `alpha`         | Concentration: `Dirichlet(α)`             | `alpha` is a vector; support is the simplex |
| TensorFlow Probability (TFP) | `tfp.distributions.Dirichlet` | `concentration` | Concentration: `Dirichlet(concentration)` | Vector parameter; support is the simplex    |
| R                            | `stats::rdirichlet`           | `n`, `alpha`    | Concentration: `Dirichlet(α)`             | `alpha` is a vector; support is the simplex |
| Julia (Distributions.jl)     | `Distributions.Dirichlet`     | `α`             | Concentration: `Dirichlet(α)`             | `α` is a vector; support is the simplex     |

**Key Observations:**

* **Parameter Naming Conventions:** Mostly consistent across platforms (`alpha`, `α`, `concentration`).
* **Support:** Always defined on the probability simplex.

**Potential Pitfalls:**

* **Misunderstanding Support:** Using the Dirichlet outside the simplex is invalid.
* **Inconsistent Parameterization:** Minor syntax differences may still cause portability issues.

---

## 4. Poisson Distribution

| Platform                     | Function / Class            | Parameters    | Parameterization      | Notes                    |
| ---------------------------- | --------------------------- | ------------- | --------------------- | ------------------------ |
| SciPy (Python)               | `scipy.stats.poisson`       | `mu`          | Rate: `Poisson(μ)`    | Mean = Variance = `μ`    |
| TensorFlow Probability (TFP) | `tfp.distributions.Poisson` | `rate`        | Rate: `Poisson(rate)` | Mean = Variance = `rate` |
| R                            | `stats::rpois`              | `n`, `lambda` | Rate: `Poisson(λ)`    | Mean = Variance = `λ`    |
| Julia (Distributions.jl)     | `Distributions.Poisson`     | `λ`           | Rate: `Poisson(λ)`    | Mean = Variance = `λ`    |

**Key Observations:**

* **Parameter Naming Conventions:** Different symbols (`mu`, `rate`, `lambda`, `λ`) for the same rate parameter.
* **Mean and Variance:** All libraries enforce mean = variance = rate.

**Potential Pitfalls:**

* **Misunderstanding Mean–Variance Equality:** Incorrect assumptions may break inference pipelines.
* **Inconsistent Parameterization:** Minor naming differences may confuse cross-library scripts.

---

