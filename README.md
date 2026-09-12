# E-Commerce Voice of the Customer (VoC) Pipeline

![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)
![Machine Learning](https://img.shields.io/badge/Machine%20Learning-Scikit--Learn-orange.svg)
![NLP](https://img.shields.io/badge/NLP-Hugging%20Face-yellow.svg)
![Database](https://img.shields.io/badge/Database-SQLite3-lightgrey.svg)
![Business Intelligence](https://img.shields.io/badge/BI-Power%20BI-yellow.svg)

An end-to-end Data Engineering, Natural Language Processing, and Business Intelligence project that transforms e-commerce customer reviews into actionable sentiment insights using a Medallion Architecture.

---

## Project Overview

The **E-Commerce Voice of the Customer (VoC) Pipeline** processes 23,000+ customer reviews to uncover customer satisfaction trends, identify negative feedback, and support data-driven product improvement decisions.

The pipeline integrates structured customer and product information with unstructured review text. It applies multiple NLP techniques, including VADER, TextBlob, and DistilBERT, and uses a Random Forest meta-classifier to generate sentiment predictions.

The processed data is organized into Bronze, Silver, and Gold layers, stored in a local SQLite data warehouse, and visualized through an interactive Power BI dashboard for executive-level analysis.

### Key Objectives

* Build a scalable, modular data processing pipeline.
* Clean and transform structured and unstructured customer review data.
* Extract sentiment features using traditional NLP and deep learning.
* Train a machine learning meta-classifier for sentiment classification.
* Model processed data in a relational SQL data warehouse.
* Develop interactive Power BI dashboards for business insights.
* Enable root-cause analysis of negative customer feedback.

---

## Executive Dashboard

![Dashboard Screenshot](dashboard.png)

The Power BI dashboard provides insights into customer sentiment, review volume, product categories, customer demographics, and potential sources of negative feedback.

> **Note:** Place your actual dashboard screenshot in the repository root and name it `dashboard.png`.

---

## System Architecture

This project follows the **Medallion Architecture**, separating raw ingestion, data transformation, and analytics-ready data into distinct layers.

```text
                    ┌──────────────────────────┐
                    │     Raw CSV Dataset      │
                    │   E-Commerce Reviews      │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │      BRONZE LAYER        │
                    │ Ingestion & Data Quality  │
                    │ Structured / Unstructured │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │       SILVER LAYER        │
                    │ Cleaning & NLP Features   │
                    │                            │
                    │ VADER | TextBlob           │
                    │ DistilBERT | Random Forest │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │        GOLD LAYER         │
                    │ SQLite Data Warehouse     │
                    │ SQL Joins & Data Mart     │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │       VALUE LAYER         │
                    │     Power BI Dashboard    │
                    │ KPIs & Root-Cause Analysis│
                    └──────────────────────────┘
```

### Data Flow

1. Raw customer review data is ingested from CSV files.
2. The Bronze layer separates structured attributes from unstructured review text.
3. The Silver layer cleans the data and generates sentiment features.
4. A Random Forest meta-classifier combines NLP outputs for sentiment prediction.
5. The Gold layer stores processed data in SQLite and builds an analytics data mart.
6. Power BI consumes the Gold layer to deliver interactive business intelligence.

---

## Data Pipeline Layers

### 1. Bronze Layer — Data Ingestion & Separation

The Bronze layer is responsible for ingesting raw customer review records and organizing them into structured and unstructured datasets.

**Key activities:**

* Ingests 23,000+ customer review records.
* Separates structured customer, product, and rating attributes.
* Extracts unstructured review text for NLP processing.
* Handles missing and corrupted review text entries.
* Preserves review identifiers for downstream data integration.

**Structured attributes include:**

* Customer age
* Clothing ID
* Department
* Rating
* Recommendation status

**Unstructured attributes include:**

* Review ID
* Raw customer review text

**Script:** `scripts/1_batch_ingestion.py`

---

### 2. Silver Layer — NLP Transformation & Meta-Classifier

The Silver layer transforms raw customer feedback into machine learning features and sentiment predictions.

To reduce reliance on a single sentiment analysis technique, the pipeline combines three independent NLP approaches.

#### NLP Models

| Model      | Purpose                                                 |
| ---------- | ------------------------------------------------------- |
| VADER      | Lexicon-based sentiment intensity analysis              |
| TextBlob   | Polarity and subjectivity analysis                      |
| DistilBERT | Contextual sentiment analysis using a transformer model |

#### Feature Engineering

Each review is processed through the NLP models to generate sentiment-related features, such as polarity scores, compound sentiment scores, and transformer-based sentiment outputs.

These features are combined into a unified dataset for machine learning.

#### Random Forest Meta-Classifier

A Random Forest classifier is trained using the extracted NLP features and customer rating-based sentiment labels.

**Reported model accuracy:** 84.9%.

The classifier predicts two sentiment categories:

* Positive
* Negative

> The reported accuracy should be supported by the model's evaluation output. The exact train/test split, labeling rule, and evaluation methodology should be documented in the implementation script or experiment notes.

**Compute environment:** Google Colab with an NVIDIA T4 GPU.

**Script:** `scripts/2_meta_classifier_silver.py`

---

### 3. Gold Layer — Data Warehousing & ELT Modeling

The Gold layer converts processed data into a structured, analytics-ready format.

**Key activities:**

* Loads cleaned customer and sentiment data into SQLite.
* Maintains relational tables for structured and NLP-derived information.
* Uses `review_id` as the integration key.
* Executes SQL `JOIN` operations to combine datasets.
* Creates a consolidated analytics data mart named `gold_product_analytics`.

The data mart is designed to simplify Power BI data ingestion and support efficient analytical queries.

**Scripts:**

* `scripts/3_data_warehouse_gold.py`
* `scripts/4_extract_for_powerbi.py`

---

### 4. Value Layer — Power BI Analytics

The Value layer presents processed data through an interactive Power BI dashboard.

#### Dashboard Features

* Total review volume
* Negative feedback percentage
* Average AI sentiment index
* Department-level sentiment analysis
* Product category comparisons
* Customer age demographic analysis
* Interactive cross-filtering
* Root-cause analysis using a Decomposition Tree

The dashboard enables stakeholders to identify high-volume product categories with negative sentiment and explore potential contributing factors.

---

## Business Insights

The following insights are based on the project's reported analytical results.

### 1. Volume vs. Performance Disconnect

High-volume categories, including Dresses and Knits, show differences in average AI sentiment compared with lower-volume categories such as Lounge and Outerwear.

This helps identify product categories that may require closer quality assurance and customer feedback analysis.

### 2. Overall Sentiment Health

The ensemble sentiment analysis reported a **22.25% negative review rate**, representing approximately 5,037 negative reviews in the analyzed dataset.

This metric provides a high-level view of customer dissatisfaction across product lines.

### 3. Granular Root-Cause Analysis

The Power BI Decomposition Tree allows users to drill down from overall negative sentiment into departments and product classes.

For example:

```text
Total Negative Reviews
        │
        ▼
     Department
        │
        ▼
       Class
        │
        ▼
  Product-Level Analysis
```

This supports the investigation of recurring product issues and potential improvement opportunities.

### 4. Demographic Sentiment Patterns

The dashboard enables sentiment comparisons across customer age groups, including customers aged 18 through 90+.

These comparisons can help determine whether negative feedback is concentrated in particular demographic segments.

> Any claim of equal product impact across all demographics should be validated using the actual sentiment distribution and statistical analysis.

---

## Technology Stack

| Category                    | Technologies                          |
| --------------------------- | ------------------------------------- |
| Programming Language        | Python 3.9+                           |
| Data Manipulation           | Pandas, NumPy                         |
| Natural Language Processing | NLTK VADER, TextBlob                  |
| Deep Learning               | Hugging Face Transformers, DistilBERT |
| Machine Learning            | Scikit-Learn, Random Forest           |
| Deep Learning Framework     | PyTorch                               |
| Compute Environment         | Google Colab, NVIDIA T4 GPU           |
| Data Storage                | SQLite3                               |
| Data Modeling               | SQL, Relational Data Warehousing      |
| Business Intelligence       | Microsoft Power BI, DAX               |

---

## Repository Structure

```text
ecommerce-voc-pipeline/
│
├── data_lake/
│   ├── raw_bronze/
│   │   └── raw_reviews.csv
│   │
│   ├── processed_silver/
│   │   └── sentiment_features.csv
│   │
│   └── gold_mart/
│       └── gold_product_analytics.csv
│
├── scripts/
│   ├── 1_batch_ingestion.py
│   ├── 2_meta_classifier_silver.py
│   ├── 3_data_warehouse_gold.py
│   └── 4_extract_for_powerbi.py
│
├── shoptrend_warehouse.db
├── dashboard.png
├── VoC_Dashboard.pbix
├── requirements.txt
└── README.md
```

> Update the example dataset filenames to match the actual files in your repository.

---

## Installation & Setup

### Prerequisites

* Python 3.9 or higher
* Git
* Power BI Desktop
* Google Colab or a GPU-enabled environment for transformer-based NLP processing

### 1. Clone the Repository

```bash
git clone https://github.com/YourUsername/Your-Repo-Name.git
cd Your-Repo-Name
```

Replace the repository URL with your actual GitHub repository URL.

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

**Windows:**

```bash
.venv\Scripts\activate
```

**macOS / Linux:**

```bash
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install pandas numpy scikit-learn nltk textblob transformers torch tqdm
```

For reproducible installation, create a `requirements.txt` file containing the project's exact dependency versions.

### 4. Download NLP Resources

If VADER is used, download the required NLTK resource:

```python
import nltk

nltk.download("vader_lexicon")
```

If TextBlob requires additional corpora in your environment, install the required resources according to its setup instructions.

---

## How to Run the Pipeline

### Step 1: Prepare the Dataset

Place the raw e-commerce customer review CSV file in:

```text
data_lake/raw_bronze/
```

Ensure that the dataset contains the columns expected by the ingestion script, including review text, review ID, rating, and relevant customer or product attributes.

### Step 2: Run Bronze Ingestion

```bash
python scripts/1_batch_ingestion.py
```

This step ingests the raw dataset and generates the separated structured and unstructured data required for subsequent processing.

### Step 3: Run Silver NLP Transformation

```bash
python scripts/2_meta_classifier_silver.py
```

This step:

* Cleans review text.
* Generates VADER sentiment features.
* Generates TextBlob polarity features.
* Runs DistilBERT sentiment analysis.
* Trains the Random Forest meta-classifier.
* Saves processed sentiment data.

A GPU-enabled environment is recommended for faster DistilBERT inference.

### Step 4: Build the Gold Data Warehouse

```bash
python scripts/3_data_warehouse_gold.py
```

This step creates the SQLite database, loads the processed tables, and executes the required SQL transformations and joins.

### Step 5: Export the Analytics Data Mart

```bash
python scripts/4_extract_for_powerbi.py
```

This step exports the consolidated analytics data for Power BI consumption.

### Step 6: Open the Power BI Dashboard

1. Open `VoC_Dashboard.pbix` in Power BI Desktop.
2. Verify the data source connection.
3. Refresh the dataset.
4. Explore the KPIs, sentiment trends, category comparisons, and Decomposition Tree.

---

## Model Evaluation

The Random Forest meta-classifier reportedly achieved an accuracy of **84.9%**.

For a complete and reproducible evaluation, the project should document:

* Dataset size used for training and testing.
* Train/test split ratio.
* Sentiment label generation methodology.
* Feature columns used by the classifier.
* Accuracy, precision, recall, and F1-score.
* Confusion matrix.
* Random seed and model hyperparameters.

This ensures that model performance can be independently verified.

---

## Data Warehouse Design

The SQLite data warehouse acts as the Gold layer storage system.

A simplified relational design is shown below:

```text
┌───────────────────────────┐
│      review_details       │
├───────────────────────────┤
│ review_id (PK)            │
│ customer_age              │
│ clothing_id               │
│ department                │
│ rating                    │
│ recommendation            │
└──────────────┬────────────┘
               │
               │ review_id
               ▼
┌───────────────────────────┐
│     sentiment_features    │
├───────────────────────────┤
│ review_id (FK)            │
│ vader_score               │
│ textblob_polarity         │
│ distilbert_score          │
│ predicted_sentiment       │
└──────────────┬────────────┘
               │
               ▼
┌───────────────────────────┐
│   gold_product_analytics  │
├───────────────────────────┤
│ Consolidated analytical   │
│ data mart for Power BI    │
└───────────────────────────┘
```

The exact schema should match the SQL table definitions implemented in the repository.

---

## Key Deliverables

| Deliverable                | Description                                     |
| -------------------------- | ----------------------------------------------- |
| Bronze ingestion script    | Raw data ingestion and separation               |
| Silver NLP script          | Sentiment feature extraction and classification |
| Gold warehouse script      | SQLite data warehouse creation                  |
| Power BI extraction script | Analytics data mart export                      |
| SQLite database            | Processed relational data                       |
| Power BI report            | Interactive business intelligence dashboard     |
| Dashboard screenshot       | Static preview for GitHub                       |

---

## Future Enhancements

* Add automated data validation and schema checks.
* Introduce incremental batch processing for new reviews.
* Implement automated pipeline orchestration using Apache Airflow.
* Deploy the NLP inference pipeline as an API.
* Add topic modeling to identify recurring customer complaints.
* Use Named Entity Recognition to extract product attributes.
* Implement model monitoring and sentiment drift detection.
* Migrate the SQLite warehouse to a cloud data warehouse.
* Add automated Power BI dataset refresh.
* Introduce CI/CD for pipeline testing and deployment.

---

## Author

**Tanya Bodhwani**

Data Engineer | Analytics Engineer

* [LinkedIn](https://www.linkedin.com/in/tanyabodh/)
* [GitHub](https://github.com/tanyaa15/)

---

## License

This project is intended for educational, portfolio, and analytical demonstration purposes.

Add an appropriate open-source license, such as the MIT License, if you plan to distribute the project publicly.
