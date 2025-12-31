# Two-Stage Fraud Detection Under Extreme Class Imbalance

## Overview

This project explores the design of a two-stage fraud detection system for highly imbalanced transaction data. Rather than optimizing a single classifier in isolation, the focus is on system-level decision making: balancing fraud prevention effectiveness against customer impact and operational constraints.

The final solution uses a high-recall screening model to identify potentially fraudulent transactions, followed by a precision-focused model that refines decisions near the classification boundary. Thresholds are selected explicitly based on business tradeoffs rather than default probability cutoffs.

This project emphasizes decision design, thresholding, and evaluation under extreme imbalance, rather than leaderboard performance.

## Dataset

The analysis uses the Credit Card Fraud Detection dataset, originally published by European cardholders and made publicly available on Kaggle.

Dataset characteristics:

~284,000 transactions

Fraud rate: ~0.17%

## Features:

Time: seconds elapsed from the start of data collection

V1–V28: anonymized PCA components

Amount: transaction amount

Class: fraud label (1 = fraud, 0 = non-fraud)

Source:
https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud

Notes on data limitations:

No account or card identifiers

No transaction sequencing per customer

Models operate at the transaction level only

Behavioral escalation cannot be directly observed

These limitations are explicitly considered in model design and interpretation.

## Problem Framing

Fraud detection presents two key challenges:

Extreme class imbalance
Accuracy is not a meaningful metric; precision–recall tradeoffs dominate.

Asymmetric costs

False negatives → financial loss

False positives → customer friction and operational cost

A practical solution must optimize decision thresholds, not just model scores.

Modeling Approach
Two-Stage Architecture

Stage 1: High-Recall Screening

Objective: catch the vast majority of fraud

Conservative threshold

Low-risk transactions pass immediately

Flagged transactions move to Stage 2

Stage 2: Boundary Refinement

Objective: maximize precision on a smaller subset

Uses a histogram-based gradient boosting model

Multiple thresholds evaluated to understand tradeoffs:

Automatic block

Customer contact / step-up verification

Allow

This structure mirrors how fraud systems are typically deployed in practice.

## Model Selection & Evaluation
Why Histogram-Based Gradient Boosting (HGB)

Several models were evaluated, including logistic regression and tree-based methods.
HistGradientBoostingClassifier was selected due to:

Strong performance on numeric, PCA-based features

Smooth and stable probability estimates

Significantly faster training than Random Forests under cross-validation

Better suitability for iterative threshold analysis

## Metrics Used

Primary: Precision–Recall AUC

Secondary: ROC AUC

Operational: Precision at fixed recall targets

System-level: End-to-end fraud capture and false-positive rates

Out-of-fold (OOF) predictions were used throughout to avoid optimistic bias when selecting thresholds.

Key Results (Selected Operating Point)

At the chosen operating thresholds:

Automatic blocking

~0.002% of legitimate transactions blocked

Customer contact / step-up

~0.019% of transactions flagged

Overall fraud detection

~95.3% of fraudulent transactions identified through automated blocking or downstream intervention

End-to-end automated capture

~75% of fraud by transaction count

~75% of fraud by dollar value

These results reflect a precision-oriented design, prioritizing customer experience while still materially reducing fraud exposure.

## Interpretation & Tradeoffs

The system does not attempt to block all fraud automatically.

Some mid-range fraud transactions are intentionally allowed through to avoid excessive false positives.

Early detection of low-value fraud can still prevent downstream losses via customer alerts and card shutdowns, even when individual transactions are not blocked.

This reflects a realistic tradeoff between fraud prevention and customer friction rather than a failure of the model.

## Limitations & Future Improvements

The dataset lacks account-level sequencing; behavioral modeling is not possible.

Optimization is transaction-based, not explicitly cost-weighted.

Future extensions could include:

Account-level features

Dollar-weighted loss functions

Amount-aware or tiered decision policies

Review-queue optimization

## Takeaway

This project demonstrates that effective fraud detection is a decision-system design problem, not just a modeling exercise. By separating screening from refinement and explicitly selecting thresholds based on operational goals, it is possible to build systems that perform well under extreme imbalance while remaining practical to deploy.
