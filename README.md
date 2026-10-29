# MS-09-CAP-03-SA: Income and Sex in the UCI Adult Data

## Overview

I analyzed the UCI Adult (Census Income) dataset (Becker & Kohavi, `1996`) to test whether sex is associated with earning over $50K.
Repository: https://github.com/intend-dev/MS-09-CAP-03-SA

## Repository

- `analysis.ipynb`: executed notebook with the analysis and figures
- `Statistical_Analysis_Report.md` and `Statistical_Analysis_Report.pdf`: report and print-ready report
- `figures/`: four PNG figures from the notebook
- `adult.csv`: UCI Adult data
- `requirements.txt`: pinned package versions

## Reproduce

`requirements.txt` was generated with `pip freeze`, so install its pinned packages with `pip install -r requirements.txt` in an activated Python `3.12` environment.
The `uv` commands below show the same setup with `uv`, the alternative used for this rebuild:

```powershell
uv venv --python 3.12 .venv
uv pip install --python .venv\Scripts\python.exe -r requirements.txt
.venv\Scripts\python.exe -m jupyter nbconvert --to notebook --execute --inplace analysis.ipynb
```

If you regenerate `requirements.txt`, avoid PowerShell `>` because it writes UTF-16.
The notebook reads `adult.csv` from the same folder and needs neither network access nor credentials.

## Dataset

- [Adult (Census Income)](https://archive.ics.uci.edu/dataset/2/adult) from the UCI Machine Learning Repository
- `32,561` rows and `15` columns
- Missing-value columns: `workclass`, `occupation`, and `native-country`
- `adult.csv` checksum: `3319b2fef796cbf96ef520c0daf10b9d90c96ab68a2db2589daefc4ed175d11a`

## License

Shared for portfolio and review purposes only, no reuse license is granted, and no license file is included.
The dataset is public data from the UCI Machine Learning Repository under its own terms.
