---
title: "Equal Risk Weighted Benchmarks / On the Properties of Equally-Weighted Risk Contributions Portfolios"
authors:
  - "Sébastien Maillard"
  - "Thierry Roncalli"
  - "Jérôme Teiletche"
year: 2008
---

# Equal Risk Weighted Benchmarks / On the Properties of Equally-Weighted Risk Contributions Portfolios

## Bibliographic Header
| Field | Detail |
|------|--------|
| Date | 2008 working paper (SSRN); published Journal of Portfolio Management / related outlets 2010 as ERC portfolios |
| Note on extraction | text extraction failed repeatedly (gateway / readable=false, size 573919 bytes). Summary synthesized from the authors' canonical ERC paper of the same year/authors/topic. |
| Core idea | **Equal Risk Contribution (ERC)** portfolios equalize each asset's contribution to portfolio volatility; sit between 1/N and minimum-variance; unique solution under mild conditions; analytic results for 2-asset and equicorrelation cases |

## Problem / Motivation
Cap-weighted indices concentrate risk in a few names/sectors. Minimum-variance (MV) portfolios are unstable and extreme. Equal-weight (EW, 1/N) improves diversification of dollars but not of risk when volatilities differ. Risk-parity / equal risk contribution seeks weights $w$ such that each asset contributes the same amount to total portfolio risk, producing a middle ground between EW and MV that is more balanced than MV and more risk-aware than EW.

## Risk Contribution Definition
Portfolio variance $\sigma^2(w)=w'\Sigma w$, volatility $\sigma(w)=\sqrt{w'\Sigma w}$. Marginal risk contribution (MRC) of asset $i$:
$$
\frac{\partial\sigma}{\partial w_i}=\frac{(\Sigma w)_i}{\sigma(w)}.
$$
**Total risk contribution (TRC):**
$$
\mathrm{TRC}_i=w_i\frac{\partial\sigma}{\partial w_i}=\frac{w_i(\Sigma w)_i}{\sigma(w)}.
$$
Euler theorem: $\sum_i\mathrm{TRC}_i=\sigma(w)$. ERC requires
$$
\mathrm{TRC}_i=\mathrm{TRC}_j=\frac{\sigma(w)}{N}\quad\forall i,j,
$$
or equivalently $w_i(\Sigma w)_i=w_j(\Sigma w)_j$.

## Optimization Programs
ERC solves the nonlinear system above under $w>0$, $\sum w_i=1$. Equivalent optimization forms used in the paper:
$$
\min_w\sum_{i,j}\bigl(w_i(\Sigma w)_i-w_j(\Sigma w)_j\bigr)^2\quad\mathrm{s.t.}\quad w>0,\ \sum w_i=1,
$$
or a variance-minimization representation with weight × marginal constraints. Numerical solution via SQP / sequential quadratic / cyclic coordinate methods. Existence/uniqueness: under positive definite $\Sigma$ and long-only simplex, ERC weights exist and are unique.

## Analytical Special Cases
### Two assets
$$
\frac{w_1}{w_2}=\frac{\sigma_2(\rho\sigma_1+\sigma_2)}{\sigma_1(\rho\sigma_2+\sigma_1)}.
$$
If $\rho=0$, $w_i\propto1/\sigma_i$ (inverse-vol). If $\sigma_1=\sigma_2$, $w=(1/2,1/2)$.

### Equicorrelation $\rho$
Weights satisfy $w_i\propto\sigma_i^{-1}$ times a mild correction in $\rho$; as $\rho\to1$, ERC collapses toward inverse-vol; as $\rho\to0$, same. When all $\sigma$ equal, ERC$=$EW for any $\rho$.

### Relationship to MV and EW
The paper orders portfolios by concentration / risk balance:
$$
\mathrm{MV}\ \preceq\ \mathrm{ERC}\ \preceq\ \mathrm{EW}
$$
in the sense that ERC volatility lies between MV and EW volatilities, and weight Herfindahl of ERC lies between MV (concentrated) and EW (least concentrated in dollars). Diversification ratio / effective number of bets is higher for ERC than MV.

## Empirical Design (canonical results)
Asset universes typically include: equity sectors, international equity indices, multi-asset (equity/bond/commodity), and stock-level books. Comparison set: cap-weighted, EW, MV, inverse-volatility, ERC. Metrics: ex-post vol, Sharpe, max drawdown, turnover, weight stability, risk-contribution Gini / Herfindahl of TRCs.

Stylized quantitative findings (from the ERC literature of Maillard–Roncalli–Teiletche):
- ERC vol < EW vol, ERC vol > MV vol.
- ERC Sharpe often competitive with MV and better than cap-weight, with far less weight concentration than MV.
- Turnover of ERC between EW and MV.
- When correlations are homogeneous and vols differ, ERC ≈ inverse-vol; when vols similar but correlations differ, ERC differs materially from inverse-vol by up-weighting diversifiers (low average correlation names).

## Risk Parity vs ERC vs Constrained MV
"Risk parity" in industry often means equal risk to *asset classes* with leverage to a vol target. ERC is the deep, asset-level version without necessarily leveraging. Constrained MV with upper bounds can mimic ERC's diversification but lacks the equal-TRC interpretation. ERC can be seen as the solution to a specific risk-budgeting problem with equal budgets $b_i=1/N$; generalized risk budgeting allows arbitrary $b_i$ with $w_i(\Sigma w)_i=b_i\sigma(w)$.

## Limitations
- Needs a covariance matrix—same estimation issues as MV (link to Bun–Bouchaud–Potters cleaning!).
- Long-only ERC cannot express negative risk contributions; highly correlated names still both get weight.
- No expected-return input—pure risk diversification; may underperform if a low-vol asset has poor reward.
- Equity single-name ERC can still be capacity-constrained in tiny names if inverse-vol tilts hard.
- Dynamic correlations: equal TRC ex-ante need not hold ex-post.

## Practical Takeaways for a Quant Investor
1. Use ERC as a **benchmark / policy portfolio** alternative to MSCI cap-weight or 60/40.
2. Always **clean $\Sigma$** before ERC (RIE/clip/LW)—garbage covariance ⇒ garbage risk budgets.
3. Compare ERC vs inverse-vol: if nearly identical, correlations are homogeneous; if not, ERC is adding true correlation diversification.
4. Generalized risk budgets $b_i$ encode views without means (e.g., more risk to equities than bonds).
5. For multi-asset risk parity, ERC at asset-class level + leverage to target vol is the industry standard; cite this paper for the math.
6. Monitor ex-post TRC drift; rebalance when TRC Herfindahl exceeds a threshold.
7. Do not expect MV-beating returns every sample—expect **better risk balance and stability**.

## Mathematical Appendix
Euler allocation: for homogeneous $\sigma(w)$, $\sum w_i\partial_i\sigma=\sigma$. Beta to portfolio: $\beta_i=(\Sigma w)_i/\sigma^2$, then $\mathrm{TRC}_i=w_i\beta_i\sigma$. ERC ⇔ $w_i\beta_i$ equal. In matrix form, $w\odot(\Sigma w)=c\mathbf{1}$ for some $c$. Log-barrier formulations and SQP are standard numerical routes. For block-equicorrelation, closed forms extend the two-asset case.
