# Vehicle Insurance Prediction - An End to End MlOps Project

An end-to-end machine learning system for vehicle insurance prediction, designed with a modular pipeline architecture and deployed using AWS services. The project covers the full lifecycle of an ML system, including data ingestion, validation, model training, evaluation, versioned storage, automated deployment, and inference serving.

---

## System Overview

The system implements a reproducible ML workflow starting from raw data stored in MongoDB and ending with a deployed prediction service running on AWS EC2. Each stage of the pipeline is isolated, configurable, and artifact-driven to support maintainability and controlled model updates.

Core stages:

* Data ingestion from MongoDB Atlas
* Schema-based data validation
* Feature engineering and transformation
* Model training and evaluation
* Model registry backed by Amazon S3
* Prediction service via Flask
* Automated CI/CD with Docker, GitHub Actions, and AWS

---

## Project Structure

```
.
├── src/
│   ├── components/              # ML pipeline components
│   │   ├── data_ingestion.py
│   │   ├── data_validation.py
│   │   ├── data_transformation.py
│   │   ├── model_trainer.py
│   │   ├── model_evaluation.py
│   │   └── model_pusher.py
│   │
│   ├── configuration/           # MongoDB and AWS configuration
│   ├── aws_storage/             # S3 interaction utilities
│   ├── pipeline/                # Training and prediction pipelines
│   ├── entity/                  # Config and artifact definitions
│   ├── utils/                   # Shared utilities
│   └── constants/               # Centralized constants
│
├── notebook/                    # EDA and MongoDB upload notebooks
├── static/                      # Static assets
├── template/                    # HTML templates
├── app.py                       # Flask application
├── demo.py                      # Pipeline execution entry point
├── requirements.txt
├── setup.py
├── pyproject.toml
└── README.md
```

---

## Technology Stack

* **Language**: Python 3.10
* **ML**: scikit-learn **3.6.1**, Pandas, NumPy
* **Backend**: Flask
* **Database**: MongoDB Atlas
* **Cloud & DevOps**:

  * AWS S3 (model registry)
  * AWS ECR (Docker image registry)
  * AWS EC2 (deployment & inference)
  * AWS IAM (access management)
  * Docker
  * GitHub Actions (CI/CD)

---

## Environment Setup

This project uses Python’s built-in virtual environment tooling.

### Create Virtual Environment

```bash
python -m venv venv
```

### Activate Virtual Environment

**Windows (PowerShell / CMD)**

```bash
.\venv\Scripts\Activate
```

**Linux / macOS**

```bash
source venv/bin/activate
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

> **Important**
> This project is tested with **scikit-learn version 3.6.1**.
> Using a different version may lead to compatibility issues during training, serialization, or inference.

---

## MongoDB Configuration

1. Create a MongoDB Atlas project and M0 cluster.
2. Add IP access: `0.0.0.0/0`.
3. Create a database user.
4. Copy the Python connection string.
5. Set the environment variable:

**Bash**

```bash
export MONGODB_URL="mongodb+srv://<username>:<password>..."
```

**PowerShell**

```powershell
$env:MONGODB_URL="mongodb+srv://<username>:<password>..."
```

---

## Data Ingestion

* Raw data is uploaded to MongoDB using a Jupyter notebook.
* The ingestion component:

  * Connects to MongoDB
  * Retrieves records in key-value format
  * Converts them into a Pandas DataFrame
  * Stores ingestion artifacts for downstream steps

Run the pipeline:

```bash
python demo.py
```

---

## Data Validation

* Dataset schema is defined in `config/schema.yaml`.
* Validation checks include:

  * Column presence
  * Data types
  * Missing values
* Validation artifacts determine whether the pipeline proceeds.

---

## Data Transformation

* Feature engineering and preprocessing are handled in the transformation stage.
* Preprocessing objects and estimators are defined in the `entity` layer.
* Transformed datasets are stored as versioned artifacts.

---

## Model Training

* Models are trained using transformed data.
* Training outputs include:

  * Serialized model
  * Performance metrics
* Training behavior is fully configuration-driven.

---

## Model Evaluation

* The newly trained model is compared against the currently deployed model pulled from S3.
* A model is promoted only if the improvement exceeds a defined threshold:

```python
MODEL_EVALUATION_CHANGED_THRESHOLD_SCORE = 0.02
```

---

## AWS Integration and Deployment

### AWS Services Used

* **IAM** – Secure credential and access management
* **S3** – Model registry and artifact storage
* **ECR** – Docker image repository
* **EC2** – Application hosting and inference runtime
* **GitHub Actions** – CI/CD orchestration with a self-hosted runner

---

### Credential Management

AWS credentials are provided via environment variables and are never hard-coded.

* Used by:

  * CI/CD pipeline (GitHub Actions secrets)
  * EC2 runtime environment
* Required variables:

  * `AWS_ACCESS_KEY_ID`
  * `AWS_SECRET_ACCESS_KEY`
  * `AWS_DEFAULT_REGION`

---

### Model Registry (Amazon S3)

* All trained models are versioned and stored in S3.
* During evaluation:

  * The current production model is pulled from S3
  * The new model is compared against it
* Approved models are pushed back to S3 using a consistent key structure.

This design enables:

* Model reproducibility
* Controlled promotion
* Rollback capability

---

### Containerization and Image Management (ECR)

* The application is packaged as a Docker image.
* CI/CD builds the image and pushes it to Amazon ECR.
* ECR serves as the source for deployment artifacts.

---

### Deployment on Amazon EC2

* An Ubuntu EC2 instance hosts the application.
* The instance:

  * Pulls the latest Docker image from ECR
  * Runs the container with required environment variables
  * Exposes port **5080** via security group rules
* The EC2 instance also acts as the self-hosted GitHub Actions runner.

Access the application at:

```
http://44.200.173.215:5000/
```

---

### CI/CD Pipeline

On each push to the repository:

1. GitHub Actions builds the Docker image
2. The image is pushed to Amazon ECR
3. The EC2 runner pulls the updated image
4. The running container is replaced

This provides consistent, repeatable deployments without manual intervention.

---

## Prediction Service

* The Flask application exposes:

  * `/predict` – Run inference
  * `/training` – Trigger model training
* Model updates can occur independently of application redeployment.

---

## Logging and Exception Handling

* Centralized logging across pipeline stages
* Custom exception handling for improved traceability
* Logs generated for ingestion, validation, training, and deployment steps

---

## Notes

* The `artifact/` directory is excluded from version control.
* Environment variables are required for MongoDB and AWS access.
* Model training can be triggered both locally and via the web interface.
