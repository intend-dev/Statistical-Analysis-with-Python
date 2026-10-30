# Statistical Analysis Report: Income and Sex in the 1994 U.S. Census

`28 October 2026`

## Overview

I analyzed the UCI Adult (Census Income) dataset (Becker & Kohavi, `1996`) to test whether sex is associated with earning over \$50K.
The dataset is publicly available at https://archive.ics.uci.edu/dataset/2/adult.
`7,841` of its `32,561` records (`24.08%`) earn over `$50K`.

This report covers the descriptive statistics, four visual models, and one chi-square test of independence.
The end result is a clear difference between men and women, with men earning  `$50K` far more often than women, `30.57%` against `10.95%`.
According to the chi-square test this gap is far too large to be sampling noise alone, with a p-value of about `1e-332`.
The effect size, Cramér's V, is `0.2160`, a small-to-moderate association.

## Dataset Description

As noted above in the overview, the analysis uses the UCI Adult training extract (Becker & Kohavi, `1996`).
This dataset is a real public extract of the `1994` U.S. Census, hosted by the UCI Machine Learning Repository, in it each row is one person, with the `income` column recording whether that person earns over `$50K` per year.

### Dataset structure

The table contains `32,561` rows and `15` columns, with the following columns and columns type:  

| Column type | Count | Columns |
|---|---|---|
| Numeric | `6` | `age`, `fnlwgt`, `education-num`, `capital-gain`, `capital-loss`, `hours-per-week` |
| Categorical | `9` | `workclass`, `education`, `marital-status`, `occupation`, `relationship`, `race`, `sex`, `native-country`, `income` |

The test uses the two categorical columns, `sex` and `income`. The descriptive statistics follow `age` and `hours-per-week`, the two numeric columns most relevant to the outcome.

#### Missing and duplicate values

Three columns carry missing values (`workclass` `1,836`, `occupation` `1,843`, and `native-country` `583`, `4,262` cells in total).
The `sex` and `income` columns are complete: all `32,561` rows carry a recorded value for both.
The test below therefore uses every row. The missing cells in the other columns do not affect it.

The load check also flags `24` exact duplicate rows. I keep them, because a census extract can legitimately hold two people with identical recorded attributes.


## Methods

### The initial screening

Lusa et al. (`2024`) describes initial data analysis as a step before inference, including reviewing missing values and examining variable distributions.
Although this paper discussion focuses on longitudinal studies, it's checks can help characterize datasets before testing.

The screening for this analysis covered:
- Column types, missing values, and exact duplicate rows.
- Descriptive statistics for `age` and `hours-per-week`.

### The hypothesis test

A chi-square test of independence was used because both `sex` and `income` are categorical.
Here chi-square approximation is appropriate when expected cell counts are sufficiently large (Agresti, `2007`); it's smallest expected count in this table is reported in the Results.

The contingency table is unweighted so each record contributes one count.
It's inferential results should not be treated as design-adjusted estimates for the U.S. population as the analysis does not adjust for the dataset's sampling weights or a complex survey design.

The hypotheses and significance level are:

- Null hypothesis (H0): `sex` and `income` are independent.
- Alternative hypothesis (H1): `sex` and `income` are associated.
- Significance level: alpha = `0.05`, set before the test.

### Direction and assumptions

In order to describe the direction of the association I used the observed counts and over `$50K` rates.
The reported `95%` confidence interval for the difference in over `$50K` rates is a normal-approximation interval for the difference in sample proportions, calculated as male minus female.

#### Independent observations
The `relationship` categories suggest that some records may come from people in the same family or household, but the available data do not identify household groupings.
I therefore cannot verify independence or adjust the analysis for possible clustering.
The reported p-value and confidence interval are unadjusted for this possibility and should be interpreted with caution (Agresti, `2007`).
The Limitations section discusses this further.

### The correction and the effect size

For a two-by-two table, SciPy's `chi2_contingency` applies the Yates continuity correction by default.
Results give SciPy's Yates-corrected statistic for comparison.
The uncorrected Pearson chi-square statistic and its p-value was also reported. 

Cramér's V was calculated from the uncorrected Pearson statistic in order to describe the association strength.
The p-value measures neither that strength nor the probability that the null hypothesis is true.
V squared is not interpreted as the percentage of income variation explained by sex.


## Results

The descriptive statistics show the shape and balance of the four relevant variables:

- `age` is right-skewed: 
  - mean is `38.5816`
  - median is `37.0`
  - standard deviation is `13.6404`
  - interquartile range is `28.0` to `48.0`
  - skewness is `0.5587`
- `hours-per-week` concentrates at a `40`-hour week: 
  - mean is `40.4375`
  - median is `40.0`
  - standard deviation is `12.3474`
  - interquartile range is `40.0` to `45.0`
  - skewness is `0.2276`
- `income` is unbalanced, with `7,841` people (`24.08%`) over `$50K`.
- `sex` is uneven, with `21,790` men and `10,771` women.

![Distribution of age.](figures/figure-1-age-distribution.png)

**Figure 1.** Distribution of age, showing the right skew.

The notebook's supporting counts show it holds `47` records, with `43` at the `90` cap, `3` at `88`, and `1` at `87`.
The data appear top-coded at `90` (respondents beyond the cap recorded as `90`), so that bump reflects the cap rather than a natural peak of older respondents.

![Hours worked per week by income bracket.](figures/figure-2-hours-by-income.png)

**Figure 2.** Hours worked per week by income bracket.

- **Median:** `40` hours in both groups.
- **Middle range:** `40` to `50` hours in the over-`$50K` group, compared with `35` to `40` hours in the at-or-below-`$50K` group.
- **Past `40` hours:** `49.2%` in the over-`$50K` group, compared with `23.2%` in the at-or-below-`$50K` group.

![Share earning over \$50K by sex.](figures/figure-3-income-by-sex.png)

**Figure 3.** Share earning over `$50K` by sex.

Figure 3 shows the income rate by sex, the clearest single view of the question. `30.57%` of men earn over `$50K` against `10.95%` of women, a gap of `19.63` percentage points.

![Correlation heatmap of the numeric variables.](figures/figure-4-correlation-heatmap.png)

**Figure 4.** Correlation heatmap of the numeric variables.

- `education-num` and `hours-per-week` are the strongest link: `0.1481`.
- `income` is categorical and outside the matrix, so this view does not directly answer the income question.
- `fnlwgt` is omitted because it is a census sampling weight, not a measured attribute.
- `capital-gain` and `capital-loss` are mostly zeros (`91.67%` and `95.33%`), so their correlations are not informative.

The chi-square test rejects independence.

- Pearson statistic: `1518.8868`; Yates-corrected: `1517.8134`; degrees of freedom: `1`.
- Uncorrected Pearson p-value: about `1e-332`, far below `1e-300`.
- Smallest expected count: `2593.76`, meeting the expected-count condition for the chi-square approximation.
- Effect size: Cramér's V `0.2160`; V squared `0.0466`.

The contingency table is:

| Sex | At or below \$50K | Over \$50K |
| --- | --- | --- |
| Female | `9,592` | `1,179` |
| Male | `15,128` | `6,662` |

The over-`$50K` rate is `19.63` percentage points higher for men than for women. Its `95%` confidence interval spans `[18.78, 20.48]` (Agresti, `2007`). 
The interval and the p-value both assume independent observations, so possible household clustering makes both approximate.

## Interpretation for a Non-Technical Audience

In this dataset, men were much more likely than women to earn over `$50K`.
About three in ten men were above the threshold, against about one in ten women, a gap of about `19.63` percentage points.
`Sex` is associated with the difference, but it does not account for every difference between the groups.

The result is an association, not proof that sex caused the difference in earnings, and it applies to the `1994` United States data. The Limitations section lists the other differences between the groups and the other caveats.

## Limitations and Potential Bias

This is an observational comparison, so the result is an association, not proof of `sex` causing the difference.

The `1994` U.S. Census extract and its undocumented sampling design limit how far the result generalizes. The `$50K` threshold is nominal and not inflation-adjusted, and `sex` is recorded as a binary field.

Missing values in three columns and the possibility of household-linked rows make the observed rates, p-value, and confidence interval approximate. A follow-up model using `education-num`, `occupation`, and `hours-per-week`, accounting for those issues, would be necessary to further analyze and get a more accurate analysis.

## References

Agresti, A. (`2007`). *An introduction to categorical data analysis* (2nd ed.). Wiley. https://doi.org/10.1002/0470114754

Becker, B., & Kohavi, R. (`1996`). *Adult* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5XW20

Lusa, L., Proust-Lima, C., Schmidt, C. O., Lee, K. J., le Cessie, S., Baillie, M., Lawrence, F., & Huebner, M. (`2024`). Initial data analysis for longitudinal studies to build a solid foundation for reproducible analysis. *PLOS ONE*, 19(5), e0295726. https://doi.org/10.1371/journal.pone.0295726

Virtanen, P., Gommers, R., Oliphant, T. E., Haberland, M., Reddy, T., Cournapeau, D., Burovski, E., Peterson, P., Weckesser, W., Bright, J., van der Walt, S. J., Brett, M., Wilson, J., Millman, K. J., Mayorov, N., Nelson, A. R. J., Jones, E., Kern, R., Larson, E., ... SciPy 1.0 Contributors. (`2020`).
SciPy 1.0: Fundamental algorithms for scientific computing in Python. *Nature Methods*, 17(3), 261-272. https://doi.org/10.1038/s41592-019-0686-2
