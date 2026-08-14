# Ecommerce Checkout Funnel & Purchase Intent Analysis

## Project Overview

This project uses ecommerce clickstream data from an electronics store to analyze how pre-purchase browsing and cart behavior relates to purchase conversion.

The goal is to build a session-level purchase intent model using behavioral signals such as product views, cart activity, product diversity, price statistics, and session duration. The project also compares a browsing-only model with a model that includes cart-stage behavior to measure how much additional predictive information appears deeper in the purchase funnel.

## Business Question

Can pre-purchase session behavior identify users who are likely to convert, and how much additional predictive value does cart-stage behavior provide beyond browsing activity alone?

## Dataset

The dataset contains event-level ecommerce activity. Each row represents a user interaction, such as a product view, cart event, or purchase.

Because the raw data is event level, it was transformed into a session-level modeling table with one row per `user_session`.

## Methodology

### 1. Initial Data Inspection

I first inspected the raw event data to understand the available fields, data types, missing values, and event structure. This helped confirm that the appropriate modeling unit was the session rather than the individual event.

### 2. Basic Cleaning

The cleaning process included:

* Removing exact duplicate rows
* Dropping rows missing critical modeling fields
* Converting `event_time` into a datetime format
* Removing invalid price values
* Preserving useful behavioral rows instead of using an overly aggressive full `dropna()`

### 3. Session-Level Feature Engineering

The target variable was created from the full cleaned session history:

* `purchased = 1` if the session eventually included a purchase
* `purchased = 0` otherwise

To reduce target leakage, predictive features were created using only activity that occurred before the first purchase event.

For purchasing sessions, only pre-purchase view and cart activity was retained. For non-purchasing sessions, all available view and cart activity was used.

Engineered features included:

* Total events
* Product views
* Cart events
* Unique products
* Unique categories
* Average product price
* Session duration
* Cart rate
* Cart-to-view ratio
* Session timing features

### 4. Exploratory Analysis

The final modeling table was reviewed to compare purchasing and non-purchasing sessions.

Key patterns included:

* Purchasing sessions had more total events, views, cart actions, unique products, and longer session durations.
* Cart-related variables showed the largest behavioral differences between purchasing and non-purchasing sessions.
* Purchasing sessions averaged approximately **1.14 cart events**, compared with approximately **0.04** for non-purchasing sessions.
* Purchasing sessions had an average cart-to-view ratio of approximately **0.73**, compared with approximately **0.02** for non-purchasing sessions.
* Average pre-purchase price was lower for purchasing sessions at approximately **$88.59**, compared with **$113.97** for non-purchasing sessions.

### 5. Modeling

Logistic regression was used because the target is binary and the model provides interpretable purchase probability scores.

Because purchasing sessions were much less common than non-purchasing sessions, the model used balanced class weights. Performance was evaluated using:

* Precision
* Recall
* F1 score
* ROC AUC
* Confusion matrix

Two models were compared:

* **Full Model:** Uses pre-purchase browsing and cart behavior
* **Browsing-Only Model:** Uses product-view behavior without direct cart-stage information

The browsing-only feature table was rebuilt using view events rather than simply removing cart columns. This prevents cart activity from indirectly influencing features such as total events, session duration, and product counts.

## Results

The full model performed strongly as a late-stage purchase intent scoring model.

### Full Model

* **ROC AUC:** 0.961
* **Purchase Recall:** approximately 0.94
* **True Positives:** 725
* **False Negatives:** 45
* **False Positives:** 674
* **True Negatives:** 18,472

The model successfully identified most purchasing sessions while accepting additional false positives because of the balanced class weighting.

### Model Comparison

* **Browsing-Only ROC AUC:** 0.663
* **Browsing + Cart ROC AUC:** 0.961
* **ROC AUC Improvement:** +0.298

Browsing behavior provided some predictive information, but model performance increased substantially once cart-stage behavior was available.

## Key Findings

* Purchase intent became much clearer deeper in the funnel. Sessions containing at least one pre-purchase cart event converted at approximately **51.8%**, compared with approximately **0.23%** for sessions without cart activity.
* Purchasing sessions demonstrated stronger pre-purchase engagement, including more views, cart actions, unique products, and longer session durations.
* Cart intensity was more informative than raw activity volume. Purchasing sessions had substantially higher cart rates and cart-to-view ratios.
* Price was not the primary separator between purchasing and non-purchasing sessions. Purchasing sessions actually interacted with lower-priced products on average.
* The browsing-only model achieved a ROC AUC of **0.663**, showing that early-funnel activity contains some purchase-intent signal.
* Adding cart-stage behavior increased ROC AUC to **0.961**, demonstrating how much stronger purchase intent becomes once users progress deeper into the funnel.

## Business Use Case

The model could help an ecommerce business identify high-intent sessions for:

* Abandoned cart campaigns
* Checkout reminders
* Personalized onsite messaging
* Remarketing campaigns
* Targeted promotional offers

A browsing-only model could be used earlier in the customer journey when behavioral signals are weaker but there is more opportunity to influence the shopper.

The cart-informed model is better suited for identifying users who have already demonstrated strong conversion intent.


## Tools Used

* Python
* pandas
* scikit-learn
* matplotlib
* Google Colab
