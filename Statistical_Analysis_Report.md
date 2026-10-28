# Statistical Analysis Report: Income and Sex in the 1994 U.S. Census

`28 October 2026`

## Overview

I analyzed the UCI Adult (Census Income) dataset (Becker & Kohavi, `1996`) to test whether sex is associated with earning over $50K. 
Of its `32,561` records, `7,841` (`24.08%`) sit over the $50K line.

This report covers the descriptive statistics, four visual models, and one chi-square test of independence. 
The result of this analysis points to a clear discrepancy between men and women: men in this data earn over $50K far more often than women, `30.57%` against `10.95%`. 
The chi-square test attributes the gap to far more than sampling noise, with a p-value of about `1e-332`. 
The effect size, Cramér's V, is `0.2160`, a small-to-moderate association.

## Dataset Description

As noted in the Overview, the analysis uses the UCI Adult training extract (Becker & Kohavi, `1996`). 
No earlier project in the program analyzed this dataset. 
It is a real public extract of the `1994` U.S. Census, hosted by the UCI Machine Learning Repository. 
Each row is one person, and `income` is the binary target of whether that person earns over $50K per year.

The table has `15` columns and `32,561` rows: `6` are numeric (`age`, `fnlwgt`, `education-num`, `capital-gain`, `capital-loss`, and `hours-per-week`) and `9` are categorical. 
The test rests on the categorical pair `sex` and `income`, and the descriptive work follows the two numeric variables most relevant to the outcome, `age` and `hours-per-week`.

Three columns carry missing values (`workclass` `1,836`, `occupation` `1,843`, and `native-country` `583`, `4,262` cells in total). 
I keep the missing values in place, because data cleanup is not the purpose of this project. 
The `sex` and `income` columns are complete: all `32,561` rows carry a recorded value for both. 
The test below therefore uses every row, and the missing cells in the other columns play no part in it.

The load check also flags `24` exact duplicate rows. I keep them, because a census extract can legitimately hold two people with identical recorded attributes.
One caution applies further down: the source is a household survey, and the `relationship` field lists husband, wife, and own-child entries, so the rows may not be independent.

## Methods

Lusa et al. (`2024`) set out the initial-data-analysis steps that should precede inference: data cleaning, a review of the missing values, and checks on the distribution and shape of each variable. 
The argument is written for longitudinal studies, but the steps apply to any dataset. 
Cleanup stays out of scope, which the Dataset Description records, so the screening before any test reduces to two steps: the load check covers the column types, the missing values, and the duplicates, and the descriptive statistics run the shape checks on `age` and `hours-per-week`. 
That screening is the full data preparation, and its results shaped the choices below: a right-skewed `age` and two unbalanced variables (`income` and `sex`).

For the question itself I use a chi-square test of independence. 
The question asks whether `sex` and `income` are associated rather than how much one predicts the other. 
Both variables are categorical, which is exactly what the test expects. 
The p-values come from a standard approximation that is only reliable when the expected counts are large, and ours (checked in the Results) sit far above that level.

The null hypothesis (H0) is that `sex` and `income` are independent. 
The alternative (H1) is that they are associated. I fixed the significance level at alpha = `0.05` before the test.

A chi-square test is non-directional: it can say whether an association exists, not which way it runs, so I read the direction of the difference from the observed counts and rates rather than expecting the test to supply them. 
A second assumption is that every row is a separate observation. 
That one is not fully true here: the source is a household survey, records can share a household, and the p-value therefore reads only as approximate. 
The Limitations section takes up that concern.

SciPy's `chi2_contingency` applies the Yates correction by default to a two-by-two table. 
I report the uncorrected Pearson statistic (the effect size builds on it) and note the corrected value in the Results. 
As an effect size I report Cramér's V, a `0`-to-`1` measure of the strength of an association (Cramér, `1946`). 
The p-value alone does not show that strength and is easy to misread as the probability that the null hypothesis is true, so the p-value and the effect size travel together (Greenland et al., `2016`).

All computations used Python `3.12.14` with pandas `3.0.6`, NumPy `2.5.3`, and SciPy `1.18.1` (Virtanen et al., `2020`), and the figures used Matplotlib `3.11.2` and Seaborn `0.13.2`. 
The notebook runs top to bottom from a clean kernel, writes the four figures itself, and prints every number the report quotes. 
`requirements.txt` pins the exact package versions, and the notebook's Reproducibility section documents the whole rebuild with `uv` step by step, from the environment creation through the executed notebook.

## Results

The descriptive statistics show the shape and balance of the four relevant variables:

- `age` is right-skewed: the mean is `38.5816`, the median is `37.0`, the standard deviation is `13.6404`, the interquartile range is `28.0` to `48.0`, and the skewness is `0.5587`.
- `hours-per-week` concentrates at a `40`-hour week: the mean is `40.4375`, the median is `40.0`, the standard deviation is `12.3474`, the interquartile range is `40.0` to `45.0`, and the skewness is `0.2276`.
- `income` is unbalanced, with `7,841` people (`24.08%`) over $50K.
- `sex` is uneven, with `21,790` men and `10,771` women.

![Distribution of age.](figures/figure-1-age-distribution.png)

**Figure 1.** Distribution of age, showing the right skew.

The curve in Figure 1 falls the way the skewness number predicts, but the last populated bin needs its own reading. 
The notebook's supporting counts show it holds `47` records, with `43` at the `90` cap, `3` at `88`, and `1` at `87`. 
The data appear top-coded at `90` (respondents beyond the cap recorded as `90`), so that bump reflects the cap rather than a natural peak of older respondents.

![Hours worked per week by income bracket.](figures/figure-2-hours-by-income.png)

**Figure 2.** Hours worked per week by income bracket.

Figure 2 shows how hours split across income. Both groups share the median of
`40` hours, but the over-$50K group's quartiles sit higher overall (lower
quartile `40` against `35`, upper quartile `50` against `40`), and `49.2%` of
that group works past `40` hours against `23.2%`.

![Share earning over $50K by sex.](figures/figure-3-income-by-sex.png)

**Figure 3.** Share earning over $50K by sex.

Figure 3 shows the income rate by sex, the clearest single view of the
question. `30.57%` of men earn over $50K against `10.95%` of women, a gap of
`19.63` percentage points.

![Correlation heatmap of the numeric variables.](figures/figure-4-correlation-heatmap.png)

**Figure 4.** Correlation heatmap of the numeric variables.

The pairwise correlations among the plotted numeric variables are all weak,
the largest `0.1481` between `education-num` and `hours-per-week`, so the
numeric variables do not move together much. That says nothing about their
link to `income`, which is categorical and outside the matrix; Figure 2 shows
that at least one numeric variable (hours) tracks income. The matrix also
leaves out `fnlwgt`, a census sampling weight rather than a measured
attribute, and carries two heavily zero-inflated columns (`91.67%` of
`capital-gain` and `95.33%` of `capital-loss` are `0`), so those correlations
are near-degenerate.

The chi-square test of independence gives a Pearson statistic of `1518.8868`
(the Yates-corrected value is `1517.8134`) with `1` degree of freedom. The
exact p-value underflows double precision, so the notebook reports its order
of magnitude, about `1e-332` (below `1e-300`). The smallest expected count is
`2593.76`, so the approximation is safe. Because the p-value is far below the
threshold, I reject the null hypothesis of independence.

The effect size is Cramér's V of `0.2160`, computed from the uncorrected
Pearson statistic, a small-to-moderate association. For a two-by-two table
Cramér's V equals phi, so V squared (`0.0466`, about `4.7%`) is the ratio of
the chi-square statistic to the sample size.

The contingency table is:

| Sex | At or below $50K | Over $50K |
| --- | --- | --- |
| Female | `9,592` | `1,179` |
| Male | `15,128` | `6,662` |

The gap in the over-$50K rate is `19.63` percentage points, with a 95%
confidence interval of `[18.78, 20.48]` from the normal approximation for a
difference in proportions. That interval assumes independent observations, so
the household clustering noted in the Methods makes it approximate, exactly as
it does the p-value.

## Interpretation for a Non-Technical Audience

In this dataset, men were much more likely than women to earn over $50K.
About three in ten men were above the threshold, against about one in ten
women, a gap of about `19.63` percentage points.

A gap of `19.63` points in a sample of `32,561` would almost never appear by
chance if sex and income were unrelated, and the chi-square test confirms the
gap (p about `1e-332`). The effect size adds the scale: `0.2160` is a
small-to-moderate value on the `0`-to-`1` scale, and sex alone explains only
about `4.7%` of the variation in who is above the line. Sex is one of many
differences between the two groups, not the whole of the difference.

The result is an association, not proof that sex caused the difference in
earnings, and it is tied to `1994` and to the United States. The Limitations
section lists the other differences between the groups and the other caveats.

## Limitations and Potential Bias

The first limitation is that the data are observational. Nobody was assigned
to be a man or a woman, so I can report an association between sex and income
but not a cause. Unmeasured factors that differ between the groups, such as
occupation, education, hours, and access to certain jobs, could contribute to
the gap.

The second limitation is the age and scope of the data. The `32,561` records
come from a `1994` U.S. Census extract rather than a current or random sample
of a defined population, so the gap is a property of this sample and may not
generalize. The income target is also a nominal $50K cut in `1994` dollars
with no inflation adjustment. `sex` is coded as a binary Male/Female field,
which bounds how far the fairness discussion can go.

The main potential source of bias is the missing and self-reported data. The
three columns with missing values (the counts are in the Dataset Description)
may be missing for reasons tied to income or sex, in which case the comparison
is biased. The kept `24` duplicates and the household clustering behind the
`relationship` field raise the same independence worry: the observations are
not all distinct draws, so the p-value and the confidence interval read as
approximate.

The result also carries a misuse risk. It describes `1994` census data and
says nothing about any individual's ability or potential. Using a demographic
attribute such as `sex` for income, credit, or hiring decisions raises equity
and privacy questions, so a difference between group rates is not, on its own,
a justification for treating individuals differently.

As a next step, a follow-up analysis could adjust for confounding by modeling
income on sex together with `education-num`, `occupation`, and
`hours-per-week`. A newer dataset could show how much of the gap persists
today.

## References

Becker, B., & Kohavi, R. (`1996`). *Adult* [Dataset]. UCI Machine Learning
Repository. https://doi.org/10.24432/C5XW20

Cramér, H. (`1946`). *Mathematical methods of statistics*. Princeton
University Press.

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