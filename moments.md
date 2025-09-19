Moments
---

# 1) Central moments (moments about the mean, $\mu_n = \mathbb{E}[(X-\mu)^n]$)

| Platform                         |                                                                                                                                                                                        Function / Class | Parameters                                                                                 | Parameterization                                                                                       | Notes                                                                                                                                                                                                                                |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **SciPy (Python)**               |                                                                                                      `scipy.stats.moment(a, order=..., axis=0, nan_policy='propagate', *, center=None, keepdims=False)` | `a` (array\_like), `order` (int), `axis`, `center` (None = use sample mean), `nan_policy`  | **Central** by default (nth moment about the mean). If `center` set, computes moment about that value. | Computes the n-th central moment for a sample/array; `center=None` ⇒ about sample mean. ([docs.scipy.org][1])                                                                                                                        |
| **TensorFlow Probability (TFP)** |                                                              `tfp.experimental.stats.RunningCentralMoments` (streaming central moments); (also `tfp.stats.variance`, `tfp.stats.stddev` for low orders) | `moment` (which orders to compute), streaming state (`num_samples`, internal accumulators) | **Central** (uses incremental / Pebay algorithm to compute moments about current mean)                 | Designed for streaming/online computation of arbitrary central moments; `tfp.stats.variance`/`stddev` available for built-in second-order estimates. Note: `RunningCentralMoments` is in `tfp.experimental.stats`. ([TensorFlow][2]) |
| **R**                            |                                                                                                                           `moments::moment(x, p = 1, central = TRUE, na.rm = TRUE)` (package `moments`) | `x` (numeric vector), `p` (order), `central=TRUE/FALSE`                                    | `central = TRUE` ⇒ central moment about sample mean                                                    | `moments::moment` returns central moments when `central=TRUE`. Package also supplies utilities to convert central↔raw. ([RDocumentation][3])                                                                                         |
| **Julia**                        | `Statistics.mean`, `Statistics.var` (sample central moments computed as `mean((x .- mean(x)).^k)`); for distributions: `mean(d)`, `var(d)`, `skewness(d)`, `kurtosis(d)` (Distributions.jl / StatsBase) | sample arrays `x`; distribution objects `d::Distribution`                                  | Central moments = moments taken about `mean(x)` or `mean(d)` for a distribution                        | For sample data compute e.g. `mean((x .- mean(x)).^k)`. Distributions.jl exposes `mean(d)`, `var(d)` etc. Use `OnlineMoments`/`OnlineStats` for streaming central-moment types. ([Julia Documentation][4])                           |


---

# 2) Non-central (raw) moments (raw moments about the origin, $\mu'_n = \mathbb{E}[X^n]$)

| Platform                                      |                                                                                                                                                 Function / Class | Parameters                                          | Parameterization                                         | Notes                                                                                                                                                                                                |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------: | --------------------------------------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SciPy (Python)**                            |                                                                          `scipy.stats.moment(a, order=k, center=0)` (or compute raw moments via `np.mean(a**k)`) | `a`, `order`, `center=0` to get moment about origin | **Raw** (about origin) when center is 0                  | `scipy.stats.moment` can compute moment about an explicit center (use `center=0` for raw/origin moment); alternatively `np.mean(a**k)` is direct and explicit. ([docs.scipy.org][1])                 |
| **TensorFlow Probability (TFP) / TensorFlow** |                                                                                                           `tf.reduce_mean(x**k)` (or `tf.math.reduce_mean(...)`) | tensor `x`, power `k`, `axis`, `keepdims`           | **Raw**: $\mathbb{E}[X^k] \approx \frac{1}{N}\sum x_i^k$ | For sample data compute `tf.reduce_mean(x**k)` (batch or sample-axis). For streaming large-scale use custom accumulators or online moments. ([TensorFlow][5])                                        |
| **R**                                         |                                                                                                      `moments::moment(x, p = k, central = FALSE)` or `mean(x^k)` | `x`, `p` (order), `central=FALSE`                   | **Raw** (about origin)                                   | `moments` supports `central=FALSE` for raw moments; `all.moments` can compute many orders. ([RDocumentation][3])                                                                                     |
| **Julia**                                     | `Statistics.mean(x .^ k)` (sample raw moment); distribution-theoretic raw moments via `mgf`/analytical formulas in Distributions.jl (use `mgf(d,t)`/derivatives) | sample `x` or distribution `d`                      | **Raw** (about origin)                                   | For empirical raw moments: `mean(x.^k)`. For theoretical raw moments use Distributions.jl functions (or compute derivatives of the MGF/CGF supplied by Distributions.jl). ([Julia Documentation][4]) |



**Parameter Naming Conventions (raw):**

* Libraries generally call the input the order (`order`, `p`, `k`); R’s `moments::moment` uses `p` and has `central=FALSE` to denote raw moments. ([RDocumentation][3])


---

# 3) Moments about *any value* $c$ (moments about an arbitrary point $c$: $\mathbb{E}[(X-c)^n]$)

| Platform                                |                                                                                    Function / Class | Parameters                 | Parameterization                                     | Notes                                                                                                                                                                                     |
| --------------------------------------- | --------------------------------------------------------------------------------------------------: | -------------------------- | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SciPy (Python)**                      |                                                          `scipy.stats.moment(a, order=k, center=c)` | `a`, `order=k`, `center=c` | Moment about user-specified center `c`               | `center` lets you compute $\mathbb{E}[(X-c)^k]$ directly without manual shifting. ([docs.scipy.org][1])                                                                                   |
| **TensorFlow Probability / TensorFlow** | `tf.reduce_mean((x - c)**k)` (or shift the tensor then use `RunningCentralMoments` on shifted data) | `x`, `c`, `k`, `axis`      | Moment about `c` computed by shifting then averaging | Compute `tf.reduce_mean((x - c)**k)` for batch data. For streaming, feed `(x - c)` into a running-moment accumulator (or use `RunningCentralMoments` on shifted input). ([TensorFlow][5]) |
| **R**                                   |                               `moments::moment(x - c, p = k, central = FALSE)` or `mean((x - c)^k)` | `x`, `c`, `p=k`            | Moment about `c` by shifting data                    | Shift-and-average pattern `mean((x - c)^k)` is straightforward; `moments` can do this by passing shifted data or adjusting `central`. ([RDocumentation][3])                               |
| **Julia**                               |      `Statistics.mean((x .- c).^k)` ; for distributions shift sample or use distribution transforms | `x`, `c`, `k`              | Moment about `c` by shifting then computing mean     | Use `mean((x .- c).^k)` for empirical moments; many packages (OnlineMoments, OnlineStats) accept pre-shifted inputs for streaming computation. ([Julia Documentation][4])                 |

**Key Observations (about c):**

* Most libraries don’t need a dedicated “moment-about-c” API — the canonical and portable approach is to **shift** the data by `c` then compute the appropriate (raw) moment of the shifted values. SciPy’s `center` arg is the convenient exception that lets you skip an explicit pre-shift. ([docs.scipy.org][1])

**Parameter Naming Conventions (about c):**

* SciPy uses `center`; other ecosystems expect you to pass shifted data (e.g., `mean((x-c)^k)` in R/Julia or `tf.reduce_mean((x-c)**k)` in TensorFlow). ([docs.scipy.org][1])

**Potential Pitfalls: Inconsistent Parameterization (about c):**

* When combining results across libraries, confirm whether each library's call was taken about the same `c` (mean vs origin vs custom); failure to standardize the center will produce mismatched numerical results. Also beware numerical instability for high-order moments when `c` is large (center near large values increases powers). ([docs.scipy.org][1])

---



[1]: https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.moment.html?utm_source=chatgpt.com "moment — SciPy v1.16.2 Manual"
[2]: https://www.tensorflow.org/probability/api_docs/python/tfp/experimental/stats/RunningCentralMoments?utm_source=chatgpt.com "tfp.experimental.stats.RunningCentralMoments"
[3]: https://www.rdocumentation.org/packages/moments/versions/0.14.1?utm_source=chatgpt.com "moments (version 0.14.1)"
[4]: https://docs.julialang.org/en/v1/stdlib/Statistics/?utm_source=chatgpt.com "Statistics - Julia Documentation"
[5]: https://www.tensorflow.org/api_docs/python/tf/math/reduce_mean?utm_source=chatgpt.com "tf.math.reduce_mean | TensorFlow v2.16.1"
