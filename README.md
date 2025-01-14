# databricks-aml-mlops-workshop

## AML Databricks MLOps Workshop - Hands-on Lab 1

### Step 0: Set up your repo
- Fork the repo at: [databricks-aml-mlops-workshop](https://github.com/classicboyir/databricks-aml-mlops-workshop)
- Clone it to your local dev environment
- Create a repo in your Azure DevOps
- Push the cloned repo into the new location: `git remote set-url origin <new path>`

### Step 1: Data lake structure
Set-up common data lake structure on the datalake
* ```raw```: this is where you can upload the sample sensor CSV file
* ```delta```: this is where you save the delta tables
* ```curated```: this is where you can save the ML-ready and ML-predictions datasets

* Download this file: https://workshopamladb.blob.core.windows.net/data/sample_data.csv?sp=r&st=2023-03-02T19:24:24Z&se=2023-03-04T03:24:24Z&spr=https&sv=2021-06-08&sr=b&sig=qKt%2BULUtIwycZVzbIeZb414ZOq9KwZOS03KFajUyNHM%3D

### Step 2: Databricks
Create a Databricks cluster with runtime ```10.4 LTS (includes Apache Spark 3.2.1, Scala 2.12)``` and ensure that these two libraries are installed:
* ```azureml-core```
* ```azureml-mlflow```

You will want to create a mount point from Databricks to the data lake using the ```utils/mount.py``` example using access key for blob storage option (simplest option without any other dependencies).

You need to run the ```utils/table.py``` to prepare the dataset used in the training step

You will also want to set up Repo integration with Azure Repos and Databricks Repos: https://learn.microsoft.com/en-us/azure/databricks/repos/repos-setup

### Step 3: Spark analysis
Run the delta table and feature engineering / modeling scripts to
* Create delta table and enable SQL-queries
* Run feature engineering and modeling
* Register model with Azure ML and mlflow

### Step 4: Service Principle Secret and ADB Token

`Service Principle`:
- Follow instructions in this link: [Create a new application secret](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#option-2-create-a-new-application-secret)

`ADB Token`
- In ADB Portal - Click on your email on top right panel
- Select "User Settings"
- Generate new token (save it somewhere as you can't see it again)

`ADB KeyVault`
- Go to: `https://<databricks-instance>#secrets/createScope`
- Set `Scope name`: `key-vault-secret`
- Select `All Users` for Manae Principle
- Paste DNS Name from your Key Vault Path



### Step 4: Azure DevOps
See the example DevOps pipeline for how to create a model training and model deployment pipeline. Here is a really good example for using the Databricks APIs: https://github.com/crflynn/databricks-api

`Project` -> `Pipelines` -> `Library`:
- Create a variable group named: `mlops-workshop`
- Add the following items:
    - adb_host
    - adb_repo_id
    - adb_secrets_scope
    - adb_sp_secret_key
    - adb_token
    - azure_svc_name
    - cluster_id
    - location
    - resource_group
    - sp_id
    - sp_secret
    - sp_tenant_id
    - subscription_id
    - workspace_name


### Retrieving the databricks values:
- Add the project to your ADB repo
- Install `databricks-api` locally: `pip install databricks-api`
- Run `utils/prep-adb.py` locally:

    `python utils/prep-adb.py --token <databricks-token> --databricks-host <http://....> --sp-secret-val <service preinciple secret value>`
    
- In the output of the `prep-adb.py` find the list of repos and copy the 'id' of the target repo. The paste the value in the Azure DevOps Library

### Set up Azure DevOps Pipeline
- Import the repo into your the Azure DevOps
- In the pipeline panel, select create pipeline
- Select `azure-pipeline-adb.yml` from the master branch of the repo
- Save and run the pipeline
 


