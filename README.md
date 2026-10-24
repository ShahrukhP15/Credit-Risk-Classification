# Credit Risk Classification

[![PyPI](https://img.shields.io/pypi/v/pycredits?label=PyPI%3A%20pycredits)](https://pypi.org/project/pycredits/) ![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white) ![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikitlearn&logoColor=white) ![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI-2088FF?logo=githubactions&logoColor=white) ![Quarto](https://img.shields.io/badge/Quarto-report-75AADB?logo=quarto&logoColor=white)

A reproducible machine learning pipeline that predicts whether a loan applicant is a good or bad credit risk, using the UCI Statlog (German Credit) dataset.

**Result:** a tuned Random Forest reached **77% accuracy** on unseen test data, against a 70% baseline.

📄 **Report:** [HTML](https://dsci-310-2024.github.io/DSCI310_Group-12_Credit-Risk-Classification/reports/credit_risk_report.html) | [PDF](reports/credit_risk_report.pdf)

## Highlights
- **One-command pipeline:** a Makefile runs every step, from downloading the data to rendering the final report.
- **Reproducible environment:** a Docker image with pinned dependencies, rebuilt and pushed to Docker Hub by GitHub Actions on every push to `main`.
- **Tested, published package:** the analysis functions live in [pycredits](https://github.com/ShahrukhP15/pycredits), a Python package on [PyPI](https://pypi.org/project/pycredits/) with 56 unit tests, Codecov coverage tracking and automated releases.

## Quick start
You only need [Docker](https://www.docker.com/products/docker-desktop/) installed.

1. Clone this repository and open a terminal in the project folder.
2. Start the container:

       docker-compose up

3. Open the link in the terminal that starts with `http://127.0.0.1:8888/lab?token=` to launch JupyterLab.
4. In a JupyterLab terminal, run the full analysis:

       make all

To delete all generated files and start fresh, run `make clean-all`. To stop, press `Ctrl + C`, then run `docker-compose rm`.

<details>
<summary>Other ways to run it</summary>

**Without docker-compose:**

    docker build -t yovindu/project --platform=linux/amd64 .
    docker run -it --rm -p 8888:8888 -v /"$(pwd)":/home/jovyan --platform=linux/amd64 yovindu/project

**In VS Code instead of JupyterLab:** from the project folder, run the command below, then `make all`. Type `exit` to leave the container.

    docker compose run --rm myapp bash

</details>

## Results
Test set performance of the Random Forest (bad credit risk = positive class):

| Accuracy | Precision | Recall | F1 score |
|---|---|---|---|
| 0.77 | 0.68 | 0.42 | 0.52 |

The model is fairly reliable when it flags someone as a bad risk, but it misses more than half of actual bad risks. Since approving a bad borrower costs a lender more than rejecting a good one, the next step would be improving recall, for example by lowering the decision threshold.

<details>
<summary>Model comparison (5-fold cross-validation accuracy)</summary>

| Model | CV accuracy |
|---|---|
| Dummy classifier (baseline) | 0.70 |
| Logistic regression (C = 0.1) | 0.74 |
| Random Forest (grid search over number of trees and depth) | 0.75 |

</details>

**A note on fairness:** the model uses Age as a feature, and older applicants tend to get better predictions. Used for real lending decisions, this could lead to age discrimination, which is prohibited under the BC Human Rights Code. Bias testing would be needed before any real-world use.

## How it works

| Step | Script | What it does |
|---|---|---|
| 1 | `scripts/download_data.py` | Downloads the dataset from UCI |
| 2 | `scripts/cleaning_and_eda.py` | Cleans the data and creates exploratory plots |
| 3 | `scripts/preprocessing.py` | 80/20 train-test split, one-hot encoding, scaling |
| 4 | `scripts/model.py` | Tunes, cross-validates and tests the models |
| 5 | `reports/credit_risk_report.qmd` | Renders the final report with Quarto |

**Data:** [Statlog (German Credit Data)](https://archive.ics.uci.edu/dataset/144/statlog+german+credit+data), 1,000 applicants with 20 features covering credit history, loan details, employment and demographics.

**Environment:** see the [Dockerfile](Dockerfile) for the base image and pinned package versions.

**Tests:** unit tests live in the [pycredits repository](https://github.com/ShahrukhP15/pycredits). To run them, clone it and run `pytest tests/`.

## Authors
Shahrukh Islam Prithibi, Sophie Yang, Yovindu Don, Jade Bouchard

Built for DSCI 310 (Reproducible and Trustworthy Workflows for Data Science) at the University of British Columbia, 2024.

See [CONTRIBUTING.md](CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) to contribute.

## License
Report: [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Code: MIT. See [LICENSE](LICENSE).

## References
- BC Human Rights Tribunal. (2023). *Personal characteristics, grounds of discrimination protected in the BC Human Rights Code*. https://www.bchrt.bc.ca/human-rights-duties/personal-characteristics/
- Costa e Silva, E., Lopes, I. C., Correia, A., & Faria, S. (2020). A logistic regression model for consumer default risk. *Journal of Applied Statistics, 47*(13-15), 2879-2894. https://doi.org/10.1080/02664763.2020.1759030
- Goraieb, E., Kumar, S., & Pepanides, T. (n.d.). *Credit risk*. McKinsey & Company. https://www.mckinsey.com/capabilities/risk-and-resilience/how-we-help-clients/credit-risk
- Hofmann, H. (1994). *Statlog (German Credit Data)* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5NC77
- Vossos, T., & Dobby, C. (2024). Wall Street to follow Canada's hot capital-relief trade. *Bloomberg*. https://www.bloomberg.com/news/articles/2024-02-22/wall-street-to-follow-canada-s-hot-capital-relief-trade
