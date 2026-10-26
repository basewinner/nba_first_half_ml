# NBA First-Half Machine Learning Model

An end-to-end machine learning workflow for modeling NBA first-half performance and generating probability-based predictions for upcoming games.

This project demonstrates a complete applied data science process: acquiring NBA game data, maintaining historical results in PostgreSQL, engineering rolling team-performance features, building a supervised machine learning model, generating first-half win probabilities, and converting those probabilities into a repeatable daily inference workflow.

The project was developed as part of Basewinner's sports analytics research and demonstrates the application of predictive modeling, data engineering, and automated decision-support techniques outside of baseball.

---

## Project Overview

The workflow is organized into four stages:

1. **NBA First-Half Data Acquisition**
   - Retrieve NBA game and first-half statistical information programmatically
   - Clean and standardize team-level results
   - Store historical information for downstream analysis

2. **Machine-Learning Dataset Construction**
   - Combine historical game results with team-performance information
   - Generate model-ready observations
   - Build rolling offensive and defensive features
   - Maintain point-in-time information for predictive analysis

3. **Model Development**
   - Train a supervised classification model
   - Estimate home-team first-half win probabilities
   - Evaluate model performance
   - Use probability output rather than only binary classifications

4. **Daily Inference**
   - Update rolling team information
   - Prepare upcoming NBA matchups
   - Score each matchup with the trained model
   - Convert model probabilities into implied moneylines
   - Classify opportunities into decision-support signals

---

## End-to-End Architecture

```text
NBA Game Data
      |
      v
Data Acquisition
      |
      v
PostgreSQL Storage
      |
      v
Historical Results
      |
      v
Rolling Feature Engineering
      |
      v
Machine Learning Model
      |
      v
First-Half Win Probability
      |
      v
Model-Implied Moneyline
      |
      v
Daily Decision Output
```

The project separates data acquisition, feature construction, model development, and recurring inference into distinct stages.

---

## Repository Structure

```text
nba_first_half_ml/
|
|-- notebooks/
|   |-- 01_get_first_half_team_scores.ipynb
|   |-- 02_build_ml_dataset.ipynb
|   |-- 03_nba_first_half_model.ipynb
|   `-- 04_daily_inference.ipynb
|
|-- sample_output/
|   `-- daily_inference_sample.csv
|
|-- README.md
|-- requirements.txt
|-- .env.example
`-- .gitignore
```

### `01_get_first_half_team_scores.ipynb`

Data-acquisition layer.

Retrieves NBA game information and first-half team scoring data and prepares it for persistent storage and downstream analysis.

### `02_build_ml_dataset.ipynb`

Feature-engineering and dataset-construction layer.

Transforms historical results into observations suitable for machine learning and combines the information needed for model training.

### `03_nba_first_half_model.ipynb`

Core machine-learning notebook.

Builds and evaluates the classification model used to estimate home-team first-half win probabilities.

### `04_daily_inference.ipynb`

Production-style inference workflow.

Updates current information, constructs matchup features, generates model probabilities, derives model-implied moneylines, and produces the daily analytical card.

---

## Feature Engineering

The inference workflow incorporates recent team performance rather than relying only on season-level results.

Example features include:

- rolling first-half points scored
- rolling first-half points allowed
- rolling scoring margin
- recent win percentage
- home/away matchup differentials
- relative recent-performance measures

For each upcoming matchup, the workflow combines the home and away team's recent information into features used by the classifier.

This provides a repeatable framework in which the same feature definitions used for historical modeling can be reconstructed for future games.

---

## Machine Learning

The prediction problem is treated as a supervised classification task.

The model estimates:

```text
P(Home Team Wins First Half)
```

The primary output is a probability rather than simply a home/away classification.

That probability can then be translated into a model-implied price and used as part of a decision-support process.

This separates the predictive model from the final decision rule:

```text
Features
   |
   v
ML Classifier
   |
   v
Home Win Probability
   |
   v
Model-Implied Moneyline
   |
   v
Decision Signal
```

---

## Sample Daily Inference

The repository includes a representative daily inference output:

```text
sample_output/daily_inference_sample.csv
```

Example:

```text
home_team,away_team,home_plus_minus,pct_margin,home_win_proba,signal,model_moneyline
MIN,HOU,4.4,0.786,0.614,CORE PLAY,-159
GSW,BKN,7.2,0.450,0.597,LEAN,-148
```

The full output also includes each team's rolling first-half scoring, points allowed, scoring margin, recent win percentage, and the date of the most recent rolling observation.

### Key Output Fields

**`home_r5_pts` / `away_r5_pts`**  
Recent rolling first-half scoring performance.

**`home_r5_allowed` / `away_r5_allowed`**  
Recent rolling first-half points allowed.

**`home_r5_margin` / `away_r5_margin`**  
Recent first-half scoring differential.

**`home_wpct` / `away_wpct`**  
Recent first-half win percentage.

**`home_plus_minus`**  
Derived matchup differential used in the daily analysis.

**`home_win_proba`**  
Machine-learning estimate of the probability that the home team wins the first half.

**`model_moneyline`**  
Model probability translated into an implied American moneyline.

**`signal`**  
Decision-support classification generated by the inference workflow, such as `CORE PLAY`, `LEAN`, or `PASS`.

---

## Data Engineering

The model is only one component of the project.

The workflow also demonstrates:

- programmatic API data acquisition
- JSON processing
- pandas-based transformation
- PostgreSQL data storage
- SQLAlchemy database integration
- historical dataset construction
- rolling feature generation
- machine-learning training and inference
- probability-to-price conversion
- recurring daily model execution
- structured CSV reporting

The result is a reusable analytics pipeline rather than an isolated modeling notebook.

---

## Model Development Philosophy

**Separate data acquisition from modeling.**  
Raw game information is collected and maintained independently from model training.

**Preserve point-in-time features.**  
Predictive features are constructed from information available before the game being modeled.

**Use rolling performance measures.**  
Recent offensive and defensive performance is transformed into model-ready matchup features.

**Use probabilities as the primary prediction.**  
Probability estimates retain more information than binary classifications and can be translated into comparable prices.

**Separate prediction from decision rules.**  
The machine-learning model generates probabilities; downstream logic converts those probabilities into analytical signals.

**Build for recurring inference.**  
The project includes the workflow required to move from historical model development to daily prediction.

---

## Technologies

- Python
- pandas
- NumPy
- scikit-learn
- PostgreSQL
- SQLAlchemy
- Jupyter
- Requests / REST APIs
- Matplotlib

---

## Configuration

Credentials and machine-specific configuration are intentionally excluded from the public repository.

The notebooks use environment variables such as:

```text
NBA_DATABASE_URL
NBA_DB_PASSWORD
BALLDONTLIE_API_KEY
```

See `.env.example` for the expected configuration.

---

## Reproducibility and Data

This repository is intended to demonstrate the modeling methodology, data-engineering workflow, feature construction, and recurring inference architecture.

Production database contents and the complete historical modeling dataset are not included. The notebooks document the process used to acquire and transform the underlying data into model features and predictions.

A representative daily inference file is included to demonstrate the final production-facing output.

---

## Why This Project Matters

This project demonstrates the ability to take a predictive sports analytics problem through the full data science lifecycle:

**API acquisition → database storage → feature engineering → machine learning → probability estimation → recurring inference → decision support**

It also demonstrates that the analytical approach used across the Basewinner portfolio is transferable between sports and prediction problems.

Combined with the MLB Starting Pitcher ML project and EPL Over 1.5 Goals project, this repository demonstrates repeated implementation of the same core data-science principles across independent datasets and domains.

---

## Related Work

The broader Basewinner analytics portfolio includes:

- MLB Starting Pitcher Machine Learning Ratings
- MLB Monte Carlo season and monthly-win simulations
- MLB run-performance analytics
- production FastAPI/PostgreSQL analytics applications
- EPL Over 1.5 Goals Machine Learning
- additional predictive sports modeling and automated analytics workflows

---

## Author

**Mark Borchard**  
Basewinner

Applied data science, machine learning, sports analytics, and predictive modeling.
