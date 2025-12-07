## Hi there 👋


## Ask me about ## Data & AI projects 💬 

- 🔭 I’m currently working on **Agentic AI Projects** and **Agentic Systems**

- 👯 I’m looking to collaborate on **hackathons, Agentic AI Systems, & Databricks Data & AI Projects**
 

<!-- 📫 ## How to reach me: ##
⚡ Fun fact: ...
-->

# Healthcare Risk Prediction App

## Overview
This project builds an end-to-end machine learning pipeline for predicting patient deterioration risk based on vitals and health data.

## Steps:
1. **Generate Data**: Simulated healthcare data generation.
2. **Data Transformation**: Clean and prepare data for ML.
3. **Model Training**: Train a risk prediction model.
4. **Model Serving**: Serve the model for real-time predictions.
5. **Databricks App**: A UI to input vitals and get predictions.
6. **Dashboard**: Monitor risk levels in a Databricks dashboard.
7. **Automated Workflow**: Automate the pipeline using Databricks Workflows.

## Requirements:
- Databricks workspace
- Python 3.x
- Databricks CLI

## Running the Project:
1. Create the data and Delta tables by running `01_create_simulated_data.ipynb`.
2. Apply the transformation pipeline in `02_transform_pipeline.ipynb`.
3. Train the model and log it to MLflow using `03_train_model.ipynb`.
4. Deploy the model with `04_model_serving_deployment.ipynb`.
5. Set up Databricks workflows and the Databricks App UI.

For full setup, follow the guide in this README.

## License:
MIT License
