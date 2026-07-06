# Project Title

AI Expense Analyzer

## Summary

AI Expense Analyzer automatically reads a bank statement CSV, categorizes every transaction (petrol, groceries, entertainment, etc.), flags unusually large expenses, and predicts next month's spending — helping people understand their money without manually sorting a single row.

## Background

Most people don't actually know where their money goes each month. Bank statements list raw transaction text like "SWIGGY ORDER 45231" or "HPCL PETROL PUMP," which tells you nothing about spending patterns unless you manually sort hundreds of rows into categories yourself.
Manually categorizing a month of transactions is tedious, so most people just... don't, and lose track of their spending habits.
A single unusually large expense (a scam charge, an impulse buy, a billing error) is easy to miss buried in a long list of transactions.
Without visibility into spending trends, it's hard to plan next month's budget — people are surprised by their own bills.
This is a problem I've personally run into: checking a bank statement and having no quick sense of "how much did I actually spend on food this month?" It's a small annoyance, but it's universal — everyone with a bank account has this problem, and it's exactly the kind of repetitive, pattern-based task that AI is well suited to remove.

## How is it used?

The intended user is anyone who wants a quick, automatic breakdown of their own spending, without setting up a budgeting habit or manually tagging transactions.
The user logs into a simple web app and uploads their bank statement as a CSV file.
The system reads every transaction, assigns it a spending category using a hybrid rule-based + machine learning classifier, and checks whether the amount is unusually large compared to the user's own history.
The user sees a dashboard: a pie chart of spending by category, a monthly trend line, a list of flagged unusual expenses, and a forecast of next month's total and per-category spending.
It's meant to be used occasionally — right after downloading a statement from a bank's website — rather than continuously, and needs no special environment beyond a web browser.

## Data sources and AI methods

The system doesn't use any external dataset — it works directly on the user's own uploaded bank statement CSV, so the "data source" is the user's own transaction history, which is also what makes the predictions personal rather than generic.
For a cold start (a brand-new user with no history yet), transaction descriptions are matched against a small hand-built set of merchant keywords (e.g. "swiggy," "hpcl," "netflix") covering common categories, and a lightweight TF-IDF + Logistic Regression classifier acts as a fallback for descriptions the keyword rules don't recognize.
Method	What it's used for
Keyword rules + TF-IDF / Logistic Regression	Categorizing each transaction (Fuel, Groceries, Entertainment, etc.)
Isolation Forest (with a z-score fallback for small histories)	Flagging unusually large expenses
Linear regression over monthly totals	Predicting next month's spending, overall and per category

## Challenges

The categorizer is only as good as its keyword list and small bootstrap training set — an unusual merchant name it hasn't seen will get miscategorized as "Others" rather than genuinely understood.
Anomaly detection is purely statistical (based on amount), so it can't tell the difference between "unusual because it's fraud" and "unusual because it's a legitimate one-off purchase, like a flight ticket."
The forecast assumes future spending will follow the same trend as past months, which breaks down around irregular events (a big move, a wedding, a medical emergency).
This project doesn't handle PDF bank statements, only CSV, which limits it for banks that only export PDFs.
There are real privacy and security considerations in handling financial data — a production version would need encryption at rest, stricter access controls, and a clear data-retention policy, none of which this student project implements.

## What next?

Add PDF statement parsing, since many banks only export PDF, not CSV.
Expand the categorizer's training data with real (anonymized) transaction descriptions to improve accuracy on unfamiliar merchants.
Move from linear regression to a seasonal forecasting model (e.g. Prophet or SARIMA) once a user has a full year of data, to capture things like higher spending around holidays.
Add budget-setting and alerts (e.g. "you're on track to overspend on Food & Dining this month").
To get there, I'd benefit from more hands-on practice with time-series forecasting methods and from feedback on real (anonymized) bank statement formats to stress-test the parser.

## Acknowledgments

Built using open-source tools: Django, Django REST Framework, MongoDB / mongoengine, Streamlit, scikit-learn, pandas, and Plotly.
Category keyword lists were written by hand based on common merchant names seen on Indian bank statements (Swiggy, Zomato, HPCL, BigBasket, etc.) and are not copied from any proprietary source.
