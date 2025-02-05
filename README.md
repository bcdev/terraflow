# MLOps Project Template

A comprehensive template for machine learning projects
incorporating MLOps practices using `Airflow`, `MLFlow`, `JupyterLab` and `Minio`.

Please note: this template has only been tested on Linux Ubuntu and it works as expected.
As we have not tested it yet on Windows, we are not sure if it works in there.

## Overview

This template provides a standardized project structure for ML initiatives at BC,
integrating essential MLOps tools:
- **Apache Airflow**: For orchestrating ML pipelines and workflows
- **MLflow**: For experiment tracking and model registry
- **JupyterLab**: For interactive development and experimentation
- **MinIO**: For local object storage for ML artifacts

## Project Structure

Currently, any files or folders marked with `*` are off-limits—no need to change, modify, 
or even worry about them. Just focus on the ones without the mark!
```
├── .github/            # GitHub Actions workflows *
├── dags/               # Airflow DAG definitions (still in progress, you have to modify it a bit for now)
├── notebooks/          # JupyterLab notebooks
├── src/                  (For new projects, it would be good to follow this standardized folder structure
                            You are of course allowed to add anything you like to it.)
│   ├── train/          # Model training
│   ├── preprocess/     # Feature engineering
│   ├── postprocess/    # Postprocess model output
│   └── utils/          # Utility functions
├── tests/              # Unit and integration tests
├── mlflow-artifacts/   # MLflow artifacts (created if you dont choose minio) *
├── mlops_run.sh        # Shell script to start MLOps services locally *
├── docker-compose.yml  # Docker compose that spins up all services locally for MLOps *
├── pipeline-config.yml # Configure your airflow DAGs (still in progress)
└── dockerfiles/        # Dockerfiles and compose files *
```

## ML Pipeline Overview
## Components

### 0. Cookiecutter
Purpose: Project scaffolding and template generation

    Provides a standardized way to create ML projects with predefined structures.
    Ensures consistency across different ML projects within BC


### 1. Apache Airflow

Purpose: Workflow orchestration

    Manages and schedules data pipelines.
    Automates end-to-end ML workflows, including data ingestion, training, deployment and re-training.
    Provides a user-friendly web interface for tracking task execution's status.

### 2. MLflow

Purpose: Experiment tracking and model management

    Tracks and records machine learning experiments, including hyperparameters, performance metrics, and model artifacts.
    Facilitates model versioning and reproducibility.
    Supports multiple deployment targets, including cloud platforms, Kubernetes, and on-premises environments.

### 3. JupyterLab

Purpose: Interactive development environment

    Provides an intuitive and interactive web-based interface for exploratory data analysis, visualization, and model development.

### 4. MinIO

Purpose: Object storage for ML artifacts

    Acts as a cloud-native storage solution for datasets and models.
    Provides an S3-compatible API for seamless integration with ML tools.

## Getting Started

### Prerequisites

- [Docker](https://docs.docker.com/engine/install/) and [Docker Compose](https://docs.docker.com/compose/install/)
- Python 3.12
- [Cookiecutter](https://cookiecutter.readthedocs.io/en/stable/installation.html#install-cookiecutter)

### Installation

1. Generate the project from template:
```bash
  cookiecutter https://github.com/bcdev/cookiecutter-mlops
```

1.1. When prompted for input, enter the details requested. If you dont provide any 
input for a given choice, the first choice from the list is taken as the default.

2. Start the services:
```bash
  chmod +x mlops_run.sh
```
```bash
  ./mlops_run.sh 
```
Use the following flags to modify the behaviour of the script
```commandline
-c -> to build docker images with/without cache; defaults to true; options [true, false]
-j -> to change the port of jupyter lab instance running; defaults to 8895
-v -> to shut down docker with/without deleting attached volumes; defaults to false; options [true, false]
```

3. Stopping the services
```bash
  ctrl + C
```

### Accessing Services

Wait for the services to start (usually take 2-3 mins, might take longer if you start it without cache)

- Airflow UI: http://localhost:8080
- MLflow UI: http://localhost:5000
- JupyterLab: Opens up JupyterLab automatically at port 8895
- Minio (Local S3): http://localhost:9000

## Usage

Deploy local inference server

Prerequisites

- [Pyenv](https://github.com/pyenv/pyenv-installer)
- Make sure standard libraries in linux are upto date.
  ```
  sudo apt-get update
  sudo apt-get install -y build-essential
  sudo apt-get install --reinstall libffi-dev
  ```
- Run these commands to export the AWS (Local Minio server running)
  ```bash
   export AWS_ACCESS_KEY_ID=minio 
   export AWS_SECRET_ACCESS_KEY=minio123
   export MLFLOW_S3_ENDPOINT_URL=http://127.0.0.1:9000
  ```
- Now we are ready for local inference server. Run this after replacing the required stuff
    ```bash
    mlflow models serve -m s3://mlflow/0/<run_id>/artifacts/<model_name> -h 0.0.0.0 -p 3333
    ```
- We can now run inference against this server on the `/invocations` endpoint,
- run `local_inference_test.py` after changing your input data.


### Development Workflow

1. Develop and experiment in JupyterLab
2. Refactor production code into the `src/` directory
3. Create tests in the `tests/` directory
4. Update CI/CD pipelines using the provided GitHub Actions workflows (if required)

### Creating ML Pipelines

1. Define your data processing and model training steps in the `src/` directory
2. Create Airflow DAGs in `dags/` to orchestrate your pipeline
3. Track experiments using MLflow in your training scripts:


## Configuration


## Acknowledgments

- [Cookiecutter](https://github.com/cookiecutter/cookiecutter)
- [Apache Airflow](https://airflow.apache.org/)
- [MLflow](https://mlflow.org/)
- [Minio](https://min.io/docs/minio/container/index.html)
- [JupyterLab](https://jupyterlab.readthedocs.io/)


## TODO:
- add tests for cookiecutter template
- add starter tests within the template
- add github worklfow for testing
- add function to create dags from a config file.
- add model deployment on remote server
- add trigger-based example dags