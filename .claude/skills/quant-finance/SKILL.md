---
name: quant-finance
description: >
  Library of detailed paper and book summaries in quantitative finance:
  momentum and trend following, factor anomalies and abnormal returns,
  portfolio construction (mean-variance, Kelly, robust and dynamic
  optimization, transaction costs), universal portfolios, market impact and
  execution, derivatives and volatility surfaces, commodity futures, fixed
  income and credit, and the statistical properties of returns (correlation
  cleaning, country/industry factors). Use this whenever the user is
  researching, designing, backtesting, implementing, reviewing, or debugging
  a trading strategy, factor, portfolio optimizer, risk model, or pricing
  model, or asks what the literature says about one, even if they don't name
  the skill, a paper, or an author.
---

# Quant Finance Reference

A routing layer over paper summaries. Pick the folder that matches the task,
find the relevant papers in it, and read only those. Never load a whole
folder.

## Which folder to open

Paths are relative to this skill's directory.

| If the task involves... | Folder |
|---|---|
| cross-sectional or time-series momentum, short-term reversal, momentum crashes, volatility scaling, factor momentum, carry | `momentum/` |
| CTAs, managed futures, moving-average rules, trend convexity, trend vs. options | `trend-following/` |
| factor zoo, accruals, asset growth, low-vol / betting against beta, idiosyncratic volatility, shorting premium | `anomalies/` |
| factor investing, value, quality, alternative risk premia, technical trading rules, pairs trading and stat arb, pricing kernels, performance measurement, anomaly replication | `abnormal-returns/` |
| mean-variance and its estimation error, shrinkage and Bayes-Stein, Black-Litterman, 1/N, risk parity, the fundamental law of active management, VaR and drawdown constraints, stochastic portfolio theory, robust or ambiguity-averse optimization, Kelly sizing, dynamic or multi-period allocation, transaction costs in optimization, active management | `portfolio-construction/` |
| Cover's universal portfolios, log-optimal growth, Kelly criterion theory, online portfolio selection, side information | `universal-portfolios/` |
| market impact models, execution costs and risk, limit order books, no-dynamic-arbitrage, Kyle-type insider trading | `market-impact/` |
| volatility surface, local volatility, VIX term structure, option returns, tail-risk hedging, commodity futures premia and storage | `derivatives/` |
| bonds, yield curves, Treasury basis, credit risk, CDS, systematic fixed income | `yield/` |
| correlation matrix cleaning, covariance structure, country vs. industry effects, return decomposition, hedge fund return properties, econometrics | `return-properties/` |

Topics overlap (Kelly spans `portfolio-construction/` and
`universal-portfolios/`; accruals span `anomalies/` and `abnormal-returns/`;
Cover-style algorithms also appear in `trend-following/`). When unsure,
search across all folders.

## Finding the right paper

Filenames are `title-authors-year.md`, so listing a folder is its index:

```
ls <folder>/
```

For a concept rather than a title, search the summaries' contents:

```
grep -ril "<concept>" <folder>/          # or . for all folders
grep -l "^year: 199" */*.md              # e.g. by decade
```

## Reading a summary

Each file opens with YAML frontmatter (`title`, `authors`, `year`, `venue`)
and runs from about 65 to 1,400 lines. Don't read it top to bottom by default:

1. List its sections with `grep -n '^## ' <file>`.
2. Read the frontmatter plus the short orientation section: `Problem
   statement` / `Approach (short)` or `Problem / Motivation`.
3. Jump to what the task needs: `Approach (detailed)` or `Model / Methods`
   for derivations, `Results with Numbers` for empirical magnitudes,
   `Replication Pseudocode` or `Equations Quick Reference` when implementing,
   `Limitations` and `Domain of applicability` before recommending a method,
   `Practical Takeaways for a Quant Investor` for a quick verdict.

## Using the material

- Cite the paper (authors, year) when a claim, parameter choice, or formula
  comes from a summary, so the user can trace it.
- Quote numbers exactly as the summary reports them, with their sample
  period; don't extrapolate them to other markets or periods.
- If the summaries don't cover the question, say so rather than filling the
  gap from memory as if it came from the library.
