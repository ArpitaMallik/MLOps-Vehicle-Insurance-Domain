# 🚗 Vehicle Insurance Prediction – End-to-End MLOps Project

An end-to-end **production-grade machine learning system** for predicting vehicle insurance outcomes, built with a strong focus on **clean architecture, scalability, CI/CD, and cloud deployment**.

This project goes far beyond training a model in a notebook. It demonstrates how a real-world ML system is **designed, validated, trained, versioned, deployed, and monitored** in a cloud environment.

---

## Problem Overview

Insurance companies rely on accurate risk prediction to make underwriting decisions.
This project builds a **vehicle insurance prediction pipeline** that:

* Ingests raw data from **MongoDB Atlas**
* Validates and transforms data using a schema-driven approach
* Trains and evaluates ML models
* Stores and versions models in **AWS S3**
* Serves predictions via a **Flask web application**
* Deploys automatically using **Docker, GitHub Actions, AWS ECR & EC2**

---

## Key Highlights (Why Recruiters Care)

* Modular, production-ready **ML pipeline architecture**
* Strong separation of concerns (ingestion, validation, transformation, training, evaluation, serving)
* **Schema-based data validation**
* Centralized logging & custom exception handling
* **Model versioning & threshold-based evaluation**
* Full **CI/CD pipeline** with Docker + GitHub Actions
* Deployed on **AWS EC2** with containerized inference
* Supports **on-demand training via API**

This is not a toy project. This is how ML systems are built in the real world.

---

## Project Architecture

```
src/
│
├── components/        # Core ML pipeline components
│   ├── data_ingestion.py
│   ├── data_validation.py
│   ├── data_transformation.py
│   ├── model_trainer.py
│   ├── model_evaluation.py
│   └── model_pusher.py
│
├── entity/            # Configs & artifacts
├── configuration/    # MongoDB & AWS connections
├── aws_storage/      # S3 model registry utilities
├── pipeline/         # Training & prediction pipelines
├── utils/            # Common utilities
│
├── app.py             # Flask app
├── demo.py            # Pipeline execution
└── constants/         # Centralized constants
```

---

## 🛠️ Tech Stack

**Languages & Frameworks**

* Python 3.10
* Flask

**Data & Storage**

* MongoDB Atlas
* AWS S3 (Model Registry)

**Machine Learning**

* Scikit-learn
* Pandas, NumPy

**DevOps & MLOps**

* Docker
* GitHub Actions (CI/CD)
* AWS EC2
* AWS ECR
* IAM

---

## Getting Started

### 1️⃣ Project Setup

```bash
conda create -n vehicle python=3.10 -y
conda activate vehicle
pip install -r requirements.txt
```

Local packages are configured via `setup.py` and `pyproject.toml`.

---

### 2️⃣ MongoDB Setup

* Create a **MongoDB Atlas** cluster (M0 – Free Tier)
* Add IP access: `0.0.0.0/0`
* Create DB user and copy the connection string
* Set environment variable:

```bash
export MONGODB_URL="mongodb+srv://<username>:<password>..."
```

---

### 3️⃣ Data Ingestion

* Data is pushed to MongoDB via a notebook
* The ingestion pipeline:

  * Fetches data from MongoDB
  * Converts key-value records into DataFrames
  * Saves artifacts for downstream steps

Run:

```bash
python demo.py
```

---

### 4️⃣ Data Validation & Transformation

* Schema-driven validation using `config/schema.yaml`
* Ensures:

  * Correct column names
  * Data types
  * Missing value checks
* Feature engineering & preprocessing handled in transformation stage

---

### 5️⃣ Model Training & Evaluation

* Models are trained using transformed datasets
* Evaluation compares new models against existing ones
* Promotion happens **only if performance improves beyond a threshold**

```python
MODEL_EVALUATION_CHANGED_THRESHOLD_SCORE = 0.02
```

---

### 6️⃣ Model Registry (AWS S3)

* Trained models are stored in S3
* Versioned model artifacts allow rollback & reproducibility

---

## 🌐 Web Application

* Flask app serves predictions
* `/predict` – Run inference
* `/training` – Trigger model training from UI
* Static & template directories included for UI rendering

---

## 🔁 CI/CD Pipeline

Every push triggers:

1. Build Docker image
2. Push image to **AWS ECR**
3. Pull & run container on **AWS EC2**
4. Application auto-deploys

**Tools Used**

* Docker
* GitHub Actions
* Self-hosted EC2 runner
* AWS ECR + EC2

---

## 📦 Deployment

* App runs on EC2 (Ubuntu)
* Dockerized Flask app exposed on port **5000**

```
http://<EC2_PUBLIC_IP>:5080
```

---

## 🧪 Logging & Error Handling

* Centralized logging for observability
* Custom exception handling for debuggability
* Designed to survive real-world failures (bad data, connection drops, schema mismatches)

---

## 📌 Final Notes

This project showcases:

* **Software engineering discipline**
* **Machine learning best practices**
* **Cloud-native deployment**
* **Production-ready MLOps thinking**

