# Ecommerce Purchase Prediction

## Project Overview

This project uses ecommerce clickstream data from an electronics store to predict whether a user session will result in a purchase. The goal is to build a session level purchase intent model using pre purchase behavioral signals such as product views, cart activity, product diversity, price statistics, and session duration.

## Business Question

Can pre purchase session behavior be used to identify users who are likely to convert?

## Dataset

The dataset contains event level ecommerce activity. Each row represents a user interaction, such as a product view, cart event, or purchase.

Because the raw data is event level, it was transformed into a session level modeling table with one row per `user_session`.

## Methodology

### 1. Initial Data Inspection

I first inspected the raw event data to understand the available fields, data types, missing values, and event structure. This helped confirm that the correct modeling unit should be the session rather than the individual event.

### 2. Basic Cleaning

The cleaning process included:

- Removing exact duplicate rows
- Dropping rows missing critical modeling fields
- Converting `event_time` into a datetime format
- Removing invalid price values
- Preserving useful behavioral rows instead of using an overly aggressive full `dropna()`

### 3. Session Level Feature Engineering

The target variable was created from the full cleaned session history:

- `purchased = 1` if the session eventually included a purchase
- `purchased = 0` otherwise

### 4. Exploratory Analysis

The final modeling table was reviewed to compare purchasing and non purchasing sessions.

Key patterns:

- Purchasing sessions had more total events, views, cart actions, unique products, and longer session durations.
- Cart activity was the strongest purchase intent signal.
- Average price was only slightly different between purchasing and non purchasing sessions.
- Sessions with cart activity converted at a much higher rate than sessions without cart activity.

### 5. Modeling

A logistic regression model was used as the baseline model because the target is binary and the model provides interpretable purchase probability scores.

Because purchase sessions were much less common than non purchase sessions, the model used balanced class weights. Performance was evaluated using:

- Precision
- Recall
- F1 score
- ROC AUC
- Confusion matrix

## Results

The model performed well as a late stage purchase intent scoring tool.

Key model results:

- ROC AUC: approximately 0.96
- High recall for purchasing sessions
- Strong separation between sessions with and without cart activity
- Some false positives, which may be acceptable for low cost marketing interventions

The model was especially effective at identifying sessions that showed strong buying intent before purchase.

## Key Findings

- Purchase intent became much clearer once users reached deeper funnel actions. Sessions with cart activity converted at about 50%, while sessions without cart activity rarely purchased.
- Purchasing sessions showed stronger engagement before conversion, including more total events, more views, more cart actions, more unique products viewed, and longer session durations.
- Cart intensity mattered more than raw activity volume. Purchasers had much higher cart rates and cart to view ratios.
- Price was not a major separator between purchasing and non purchasing sessions. Behavioral signals were more useful than price alone.
- The model is best interpreted as a late stage purchase intent scoring tool rather than a general demand prediction model.

## Business Use Case

This model could help an ecommerce business identify high intent sessions for:

- Abandoned cart campaigns
- Checkout nudges
- Personalized offers

The main business value is prioritizing users who are already showing strong conversion readiness.

## Tools Used

- Python
- pandas
- scikit learn
- matplotlib
- Google Colab



