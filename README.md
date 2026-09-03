# zm_2503
Comparison of classical vs. modern A/B testing algorithms

# Comparative Methodology: Classical vs. Modern A/B Testing Algorithms

## 1. Report Design


* **Scope**: Theoretical and practical comparison of classical frequentist methods versus modern adaptive/Bayesian experimentation algorithms.
* **Case Selection**:
  * **Stanford (Walsh, 2019)**: Frequentist rigor, sequential validity, and the breakdown of classical testing in decentralized systems.
  * **UPenn (Miller, 2022)**: Empirical examination of real-world misuse (peeking, p-hacking) and Bayesian correctives.
  * **Aalto (Kuusisto, 2023)**: Team-level experimental workflows and operationalization in B2C e-commerce.
* **Research Questions**:
  1. *How do classical frequentist error guarantees fail under dynamic sample collection, and how do Bayesian/adaptive frameworks address this?*
  2. *What algorithmic mechanisms are emerging across large-scale industry platforms (e.g., TikTok, YouTube)?*

---


## 2. Statistical Models & Formulations

### 2.1 Frequentist Hypothesis Testing

#### Two-Sample Welch's $t$-Test (Continuous Metrics, e.g., Watch Time)

Used when population variances are unequal ($\sigma_1^2 \neq \sigma_2^2$):

$$t = \frac{\bar{X}_1 - \bar{X}_2}{\sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}}}$$

Where:
* $\bar{X}_1, \bar{X}_2$: Sample means of variants $1$ and $2$
* $s_1^2, s_2^2$: Sample variances
* $n_1, n_2$: Arm sample sizes

Degrees of freedom ($\nu$) via Welch–Satterthwaite:

$$\nu \approx \frac{\left(\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}\right)^2}{\frac{(s_1^2/n_1)^2}{n_1 - 1} + \frac{(s_2^2/n_2)^2}{n_2 - 1}}$$

*Decision Rule*: Reject $H_0: \mu_1 = \mu_2$ at significance level $\alpha$ if $|t| > t_{\text{crit}, 1-\alpha/2, \nu}$.

---

#### Two-Proportion $Z$-Test (Binary Metrics, e.g., Conversion Rate, CTR)

$$Z = \frac{\hat{p}_1 - \hat{p}_2}{\sqrt{\hat{p}(1 - \hat{p})\left(\frac{1}{n_1} + \frac{1}{n_2}\right)}}$$

Where the pooled proportion $\hat{p}$ is defined as:

$$\hat{p} = \frac{x_1 + x_2}{n_1 + n_2}$$

*Decision Rule*: Reject $H_0: p_1 = p_2$ if $|Z| > Z_{1 - \alpha/2}$ (e.g., $1.96$ for $\alpha = 0.05$).

---

### 2.2 Bayesian Inference

#### Posterior Probability of Superiority

Instead of computing $p$-values conditioned on $H_0$, calculate the direct probability that variant $A$ beats variant $B$:

$$P(\theta_A > \theta_B \mid \mathcal{D}) = \int_{-\infty}^{\infty} \int_{\theta_B}^{\infty} p(\theta_A \mid \mathcal{D}) \, p(\theta_B \mid \mathcal{D}) \, d\theta_A \, d\theta_B$$

For conjugate Beta-Binomial models where prior $\theta \sim \text{Beta}(\alpha_0, \beta_0)$:

$$\theta \mid \mathcal{D} \sim \text{Beta}(\alpha_0 + k, \, \beta_0 + n - k)$$

Where $k$ is conversions and $n$ is total impressions.

*Decision Rule*: Declare $A$ superior if $P(\theta_A > \theta_B \mid \mathcal{D}) \ge 1 - \gamma$ (typically $\gamma = 0.05 \implies 95\%$).

---

### 2.3 Adaptive Algorithms & Multi-Armed Bandits

#### Upper Confidence Bound (UCB1)

Balances exploration of uncertain variants with exploitation of winning arms:

$$\text{UCB}_i(t) = \bar{X}_i + \sqrt{\frac{2 \ln t}{n_i}}$$

Where:
* $\bar{X}_i$: Empirical mean payoff of arm $i$
* $n_i$: Number of times arm $i$ has been pulled
* $t$: Total steps across all arms ($t = \sum_j n_j$)

*Allocation Policy*:

$$a_t = \arg\max_{i} \left( \bar{X}_i + \sqrt{\frac{2 \ln t}{n_i}} \right)$$

---

#### Contextual Bandits (Personalized Allocation)

Assigns variants conditional on user covariate vectors $x \in \mathbb{R}^d$:

$$\pi^*(x) = \arg\max_{a \in \mathcal{A}} \mathbb{E}[R \mid x, a]$$

Using a linear payoff assumption ($\mathbb{E}[R \mid x, a] = x^T \theta_a$), the LinUCB ridge regression decision rule is:

$$a_t = \arg\max_{a \in \mathcal{A}} \left( x_t^T \hat{\theta}_a + \alpha \sqrt{x_t^T A_a^{-1} x_t} \right)$$

Where $A_a = D_a^T D_a + I_d$ acts as the design covariance matrix for arm $a$.

---

## 3. Platform Architectural Comparisons

| Dimension | TikTok (Split Test) | YouTube Studio (Test & Compare) |
| :--- | :--- | :--- |
| **Statistical Engine** | Frequentist $Z$/$t$-Testing | Bayesian Posterior Estimation |
| **Core Metric Target** | Conversion Efficiency (CPA, ROAS) | Total Watch Time per Impression |
| **Allocation Mechanism** | Balanced Split ($50/50$ or fixed slices) | Even Split across $2\text{--}3$ assets |
| **Bias Mitigation** | Pre-test audience isolation | Anti-clickbait objective weighting |
| **Stopping Condition** | Budget/Time exhaustion ($7\text{--}14$ days) | Posterior threshold / max 14 days |

---

## 4. References & Key Dissertations

1. **Walsh, D. J. M. (2019).** *How to Design and Analyze Online A/B Tests Within Decentralized Organizations*. Ph.D. Dissertation, Department of Statistics, Stanford University.
2. **Miller, A. P. (2022).** *Essays on the Use of A/B Testing Among E-Commerce Practitioners*. Ph.D. Dissertation, Wharton School, University of Pennsylvania.
3. **Kuusisto, N. (2023).** *Experimentation Process and Experiment Design in A/B Testing Teams*. Master's Thesis, School of Science, Aalto University.

