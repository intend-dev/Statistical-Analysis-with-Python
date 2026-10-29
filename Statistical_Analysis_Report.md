# Statistical Analysis Report: Income and Sex in the 1994 U.S. Census

`28 October 2026`

## Overview

I analyzed the UCI Adult (Census Income) dataset (Becker & Kohavi, `1996`) to test whether sex is associated with earning over $50K.
The dataset is publicly available at https://archive.ics.uci.edu/dataset/2/adult.
`7,841` of its `32,561` records (`24.08%`) earn over $50K.

This report covers the descriptive statistics, four visual models, and one chi-square test of independence.
The result is a clear difference between men and women: men in this data earn over $50K far more often than women, `30.57%` against `10.95%`.
The chi-square test says the gap is far too large to be sampling noise alone, with a p-value of about `1e-332`.
The effect size, Cramér's V, is `0.2160`, a small-to-moderate association.

## Dataset Description

As noted in the Overview, the analysis uses the UCI Adult training extract (Becker & Kohavi, `1996`).
It is a real public extract of the `1994` U.S. Census, hosted by the UCI Machine Learning Repository.
Each row is one person. The `income` column records whether that person earns over $50K per year.

The table has `15` columns and `32,561` rows: `6` are numeric (`age`, `fnlwgt`, `education-num`, `capital-gain`, `capital-loss`, and `hours-per-week`) and `9` are categorical.
The test uses the two categorical columns, `sex` and `income`. The descriptive statistics follow `age` and `hours-per-week`, the two numeric columns most relevant to the outcome.

Three columns carry missing values (`workclass` `1,836`, `occupation` `1,843`, and `native-country` `583`, `4,262` cells in total).
I keep the missing values in place, because data cleanup is not the purpose of this project.
The `sex` and `income` columns are complete: all `32,561` rows carry a recorded value for both.
The test below therefore uses every row. The missing cells in the other columns do not affect it.

The load check also flags `24` exact duplicate rows. I keep them, because a census extract can legitimately hold two people with identical recorded attributes.
One caution comes up later in the report: the source is a household survey, and the `relationship` field lists husband, wife, and own-child entries, so the rows may not be independent.

## Methods

Lusa et al. (`2024`) described the initial data analysis steps that should come before inference: data cleaning, a review of the missing values, and checks on the distribution and shape of each variable.
It's main argument is written for longitudinal studies, but the steps mentions can be applied to to any dataset.
An important notice to mention is that data cleanup needs to stay out of scope, as it was mentioned previously.
Before the test, two checks make up the initial screening.
The first one is load check that reports the column types, the missing values, and the duplicate rows.
The second one is descriptive statistics that show the shape of `age` and `hours-per-week`.
Those two checks are the whole data preparation for this project, and with them it was possible to observe that the dataset has a right-skewed `age` and two unbalanced variables (`income` and `sex`).

For the question itself I used a chi-square test of independence.
The question asks whether `sex` and `income` are associated rather than how much one predicts the other.
Both of these variables are categorical, which is exactly what the test expects.
The p-values came from a standard approximation that is reliable only when the expected counts are large (Agresti, `2007`).
As the results show, the expected counts are far above that level. 

The null hypothesis (H0) is that `sex` and `income` are independent.
The alternative (H1) is that they are associated, and I fixed the significance level at alpha = `0.05` before the test.

A chi-square test is non-directional: it can say whether an association exists, but not which way it runs, for this reason I read the direction from the observed counts and rates instead.

A second assumption is that every row is a separate observation, this assumption however not fully true here as the source is a household survey, and records can share a household, therefore for the p-value is only approximate (Agresti, `2007`), the Limitations section returns to this concern.

SciPy's `chi2_contingency` applies the Yates correction by default to a two-by-two table.
I reported the uncorrected Pearson statistic and noted the corrected value in the Results.
The effect size uses the uncorrected statistic.
As an effect size I report Cramér's V, a `0`-to-`1` measure of the strength of an association (Ben-Shachar et al., `2023`).
The p-value alone does not show that strength, and it is easy to misread as the probability that the null hypothesis is true.
I therefore report the p-value and the effect size together (Greenland et al., `2016`).

All computations used Python `3.12.14` with pandas `3.0.6`, NumPy `2.5.3`, and SciPy `1.18.1` (Virtanen et al., `2020`), and the figures used Matplotlib `3.11.2` and Seaborn `0.13.2`.
The notebook runs top to bottom from a clean kernel, writes the four figures itself, and prints every number the report quotes.
`requirements.txt` pins the exact package versions.
The notebook's Reproducibility section walks through the whole rebuild with `uv`, step by step.

## Results

The descriptive statistics show the shape and balance of the four relevant variables:

- `age` is right-skewed: the mean is `38.5816`, the median is `37.0`, the standard deviation is `13.6404`, the interquartile range is `28.0` to `48.0`, and the skewness is `0.5587`.
- `hours-per-week` concentrates at a `40`-hour week: the mean is `40.4375`, the median is `40.0`, the standard deviation is `12.3474`, the interquartile range is `40.0` to `45.0`, and the skewness is `0.2276`.
- `income` is unbalanced, with `7,841` people (`24.08%`) over $50K.
- `sex` is uneven, with `21,790` men and `10,771` women.

![Distribution of age.](figures/figure-1-age-distribution.png)

**Figure 1.** Distribution of age, showing the right skew.

The curve in Figure 1 falls the way the skewness number predicts, but the last populated bin needs a separate explanation.
The notebook's supporting counts show it holds `47` records, with `43` at the `90` cap, `3` at `88`, and `1` at `87`.
The data appear top-coded at `90` (respondents beyond the cap recorded as `90`), so that bump reflects the cap rather than a natural peak of older respondents.

![Hours worked per week by income bracket.](figures/figure-2-hours-by-income.png)

**Figure 2.** Hours worked per week by income bracket.

Figure 2 compares how weekly hours are spread between the two income groups.

- **Median:** `40` hours in both groups.
- **Middle range:** `40` to `50` hours in the over-$50K group, compared with `35` to `40` hours in the at-or-below-$50K group.
- **Past `40` hours:** `49.2%` in the over-$50K group, compared with `23.2%` in the at-or-below-$50K group.

![Share earning over $50K by sex.](figures/figure-3-income-by-sex.png)

**Figure 3.** Share earning over $50K by sex.

Figure 3 shows the income rate by sex, the clearest single view of the
question. `30.57%` of men earn over $50K against `10.95%` of women, a gap of
`19.63` percentage points.

![Correlation heatmap of the numeric variables.](figures/figure-4-correlation-heatmap.png)

**Figure 4.** Correlation heatmap of the numeric variables.

Figure 4 shows the correlations among the plotted numeric variables.

- `education-num` and `hours-per-week` are the strongest link: `0.1481`.
- `income` is categorical and outside the matrix, so this view does not directly answer the income question.
- `fnlwgt` is omitted because it is a census sampling weight, not a measured attribute.
- `capital-gain` and `capital-loss` are mostly zeros (`91.67%` and `95.33%`), so their correlations are not informative.

The chi-square test rejects independence.

- Pearson statistic: `1518.8868`; Yates-corrected: `1517.8134`; degrees of freedom: `1`.
- p-value: about `1e-332`, far below `1e-300`.
- Smallest expected count: `2593.76`, so the approximation is safe.
- Effect size: Cramér's V `0.2160`; V squared `0.0466` (`4.7%`).

The contingency table is:

| Sex | At or below $50K | Over $50K |
| --- | --- | --- |
| Female | `9,592` | `1,179` |
| Male | `15,128` | `6,662` |

The over-$50K rate is `19.63` percentage points higher for men than for women. Its `95%` confidence interval spans `[18.78, 20.48]` (Agresti, `2007`). The interval and the p-value both assume independent observations, so possible household clustering makes both approximate.

## Interpretation for a Non-Technical Audience

In this dataset, men were much more likely than women to earn over $50K.
About three in ten men were above the threshold, against about one in ten
women, a gap of about `19.63` percentage points.

A gap of `19.63` points in a sample of `32,561` would almost never appear by
chance if sex and income were unrelated, and the chi-square test confirms the
gap (p about `1e-332`). The effect size shows how strong the association is: `0.2160` is a
small-to-moderate value on the `0`-to-`1` scale, and sex alone explains only
about `4.7%` of the variation in who is above the line. Sex is part of the
difference, not all of it.

The result is an association, not proof that sex caused the difference in
earnings, and it applies to the `1994` United States data. The Limitations
section lists the other differences between the groups and the other caveats.

## Limitations and Potential Bias

This is an observational comparison, so the result is an association, not proof
of sex causing the difference.

The `1994` U.S. Census extract and its undocumented sampling design limit how
far the result generalizes. The $50K threshold is nominal and not
inflation-adjusted, and `sex` is recorded as a binary field.

Missing values in three columns and the possibility of household-linked rows make
the observed rates, p-value, and confidence interval approximate. A follow-up
model using `education-num`, `occupation`, and `hours-per-week`, accounting
for those issues, would be a useful next step.

## References

Agresti, A. (`2007`). *An introduction to categorical data analysis* (2nd
ed.). Wiley. https://doi.org/10.1002/0470114754

Becker, B., & Kohavi, R. (`1996`). *Adult* [Dataset]. UCI Machine Learning
Repository. https://doi.org/10.24432/C5XW20

Ben-Shachar, M. S., Patil, I., Thériault, R., Wiernik, B. M., & Lüdecke, D.
(`2023`). Phi, Fei, Fo, Fum: Effect sizes for categorical data that use the
chi-squared statistic. *Mathematics*, 11(9), `1982`.
https://doi.org/10.3390/math11091982

Greenland, S., Senn, S. J., Rothman, K. J., Carlin, J. B., Poole, C., Goodman,
S. N., & Altman, D. G. (`2016`). Statistical tests, P values, confidence
intervals, and power: A guide to misinterpretations. *European Journal of
Epidemiology*, 31(4), 337-350. https://doi.org/10.1007/s10654-016-0149-3

Lusa, L., Proust-Lima, C., Schmidt, C. O., Lee, K. J., le Cessie, S., Baillie,
M., Lawrence, F., & Huebner, M. (`2024`). Initial data analysis for
longitudinal studies to build a solid foundation for reproducible analysis.
*PLOS ONE*, 19(5), e0295726. https://doi.org/10.1371/journal.pone.0295726

Virtanen, P., Gommers, R., Oliphant, T. E., Haberland, M., Reddy, T.,
Cournapeau, D., Burovski, E., Peterson, P., Weckesser, W., Bright, J., van der
Walt, S. J., Brett, M., Wilson, J., Millman, K. J., Mayorov, N., Nelson, A.
R. J., Jones, E., Kern, R., Larson, E., ... SciPy 1.0 Contributors. (`2020`).
SciPy 1.0: Fundamental algorithms for scientific computing in Python. *Nature
Methods*, 17(3), 261-272. https://doi.org/10.1038/s41592-019-0686-2