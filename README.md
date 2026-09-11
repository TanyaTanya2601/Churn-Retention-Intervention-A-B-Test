## Churn/Retention Intervention A/B Test

A data science portfolio project built to demonstrate the full workflow behind a targeted customer-retention program: predicting who's at risk of churning, deciding who to intervene on, running a randomized experiment to test the intervention, and analyzing the result the way a business stakeholder would actually want it analyzed — not just "did it work," but "was it worth the money."

## Overview 

I wanted to go beyond a simple "here's a churn model" notebook. Most churn projects stop at training a classifier and reporting accuracy, and I felt like that was only half the story — it tells you who might leave, but not what to actually do about it, or whether doing something even pays off. So I built this project to walk through the full loop:

Build a model that scores every customer's risk of leaving
Use that model to decide who should actually receive a retention offer, instead of offering it to everyone
Design and run a randomized experiment to test whether the offer works
Analyze the result properly — not just "did it work," but "was it worth the money," "did it hold up statistically," and "did it work for everyone or just some customers"

I put it together this way because I wanted a project that forced me to practice interpretable modeling, proper experiment design, and business/profitability thinking all together, rather than as three separate disconnected exercises.

## DATASET

This project uses the real "Bank Customer Churn Modelling" dataset from Kaggle — customer-level data including tenure, balance, number of products, account activity, demographics, and whether the customer churned (Exited).

Dataset source: Bank Customer Churn Modelling — Kaggle [https://www.kaggle.com/datasets/aakash50897/churn-modellingcsv]

To run this notebook yourself, download Churn_Modelling.csv from the link above.

## Explanation:

1. Read the dataset

Loads the real Kaggle customer data and renames columns to consistent, readable names used throughout the rest of the notebook.

2. Build the churn-propensity model
   
Primary model: logistic regression, chosen for interpretability — the goal is to understand why the model flags someone as high-risk, not just trust an opaque score.
Comparison model: XGBoost, fit to check whether nonlinear structure meaningfully outperforms the simpler model.
Evaluation: AUC on a stratified train/test split, a leakage check confirming no feature encodes post-churn information, and a calibration curve confirming predicted probabilities are trustworthy (not just correctly ranked).

4. Define the high-risk target segment

The model is refit on the full customer base and used to score everyone. The top 20% by predicted churn risk becomes the target segment for the retention offer — concentrating spend on the customers most likely to need it, rather than a blanket promotion to the entire customer base.

5. Simulate the randomized experiment

Within the high-risk segment only, customers are randomly split 50/50 into treatment (receives the retention offer) and control. A pre-period balance check confirms the two groups are statistically comparable before the "intervention." The offer's simulated effect is then applied on top of each customer's baseline churn score.

6. Analyze the results
   
Primary metric: retention rate, tested with a two-proportion z-test
Robustness check: a regression-adjusted estimate (logistic regression with a treatment indicator plus covariates) to confirm the result holds up
Guardrail metric: cost per incrementally retained customer, and the program's overall net value — because a statistically significant lift doesn't automatically mean the program was worth running
Power check: a retrospective check confirming the segment was large enough to reliably detect an effect of this size
Segment cuts: the effect broken out by customer tenure band, to check whether it holds broadly or is concentrated in one group

## The notebook closes with a short decision-memo summary — hypothesis, design, result, and an honest list of limitations and next steps.

Tech stack
Purpose	Library
Data handling	pandas, numpy
Modeling	scikit-learn (Logistic Regression), XGBoost
Statistics / experiment analysis	statsmodels, scipy
Visualization	matplotlib

## How to run this?
Clone this repository
Install dependencies:
   pip install -r requirements.txt
Download Churn_Modelling.csv from Kaggle and update the file path in Section 1 of the notebook
Open Churn_Intervention_AB_Test.ipynb in Jupyter and run all cells
Limitations — stated honestly
The retention offer's treatment effect is simulated, not drawn from a real completed A/B test — the customer data is real, but the intervention outcome is a stated assumption, not a measured result
Novelty effects aren't modeled — a real retention offer's impact could fade with repeated exposure across campaigns
The assumed treatment effect size and the cost/value figures used in the guardrail metric are illustrative placeholders, not real bank figures
If this were deployed for real, the natural next steps would be monitoring for effect decay over time, watching for cannibalization (customers who'd have stayed anyway still claiming the offer), and re-running the power analysis before scaling to the full customer base
Project background

Built as part of preparation for Data Scientist interviews, to have a single, complete example covering interpretable modeling, experiment design, statistical rigor, and business-outcome thinking together.
