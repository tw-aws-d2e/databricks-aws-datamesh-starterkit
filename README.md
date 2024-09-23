# Stock Last Sales

The 'stock_last_sales' project is an example project to show how to implement a data product with Databricks Asset Bundles. The main job will process the data which is then published to Data Mesh Manager. It also includes implementing CI/CD using GitHub Actions. 

This bundle contains two jobs:
  - `data_ingestion_job`: simulates an input port
  - `stock_last_sales_job`: creates unity catalog, main process, publish to Data Mesh Manager, and test the Data Contract.

Project structure:
- root folder contains yml files for Databricks bundle, data contract, and data product information
- src folder contains python notebooks that are used in the jobs
- resources folder contains job yml files
  
This code is modified from [this article on building a data product with Databricks](https://www.datamesh-architecture.com/howto/build-a-dataproduct-with-databricks).

# Installation and Setup 
## Prerequisites:
In this article, we will use the new [Databricks Asset Bundles](https://docs.databricks.com/en/dev-tools/bundles/index.html) that are a great fit to implement data products.
Other tools that will be used:
  - Python 3.10
  - pip 24.2 
  - [Databricks CLI](https://docs.databricks.com/en/dev-tools/cli/tutorial.html).
  - [Data Contracts](https://cli.datacontract.com/)
  - GitHub Actions
  - [Data Mesh Manager](https://datamesh-manager.com/)

To fully run this starter kit, you need permissions for:
  - Creating job clusters, catalogs, and personal access tokens on Databricks workspace
  - Organization access in Data Mesh Manager
  - Creating Data Mesh Manager API Key

## Configuration

### 1. To set up a local Python environment, we can use venv and install the development dependencies:
```
python3.10 -m venv .venv
source .venv/bin/activate
pip install -r requirements-dev.txt
```

### 2. Set up secrets for the Data Mesh Manager
  1. On the Data Mesh Manager website, click your username in the top right corner.
  2. Go to the API Keys tab on the left side.
  3. Add API key, give it a descriptive name and copy the key.
  4. In the CLI type the following commands, hit enter after each one. It will prompt you to add the API key after the `put-secret` command. Secret names are workspace-wide and need to be unique.

```
databricks secrets create-scope <scope-name>
databricks secrets put-secret <scope-name> api_key
```
  5. In the second block of `src/publish_datamesh_manager.ipynb`, replace `"datamesh_manager"` with the name of the secret you just created.
  6. At the very bottom of `resources/stock_last_sales_job.yml` where it sets `DATAMESH_MANAGER_API_KEY`, replace `"datamesh_manager"` with the name of the secret you just created.  
### 3. Replace workspace urls
Replace the host urls and email notification settings with your own workspace url and email in `databricks.yml`, `dataproduct.yml`, and `datacontract.yml`.

### 4. Change catalog 
If you do not have permissions to the catalog listed in `datacontract.yml` under `servers: > catalog:`, change the name of the catalog and replace all instances in the other files:
  - `dataproduct.yml`
  - `/src/create_unity_catalog.ipynb`
  - `/src/prepare.ipynb`
  - `/src/stock_last_sales/main.py`

### 5. Run setup script
To simulate an input port, you will need to run the data ingestion job before running any other job.

```
databricks bundle run data_ingestion_job
```
# Basic Usage
### 1. When we are confident, we can deploy the bundle to our Databricks dev instances (manually for now):

```
databricks bundle deploy
```

### 2. And let's trigger a manual run of our workflow:
```
databricks bundle run stock_last_sales_job
```
You should see the job appear in your Databricks workspace. When `stock_last_sales_job` is finished, you will be able to see it registered in the Data Mesh Manager.

### 3. Setting up GitHub Actions:
Using GitHub Actions, we can automate deployment of the bundle every time there is a push. You must turn on GitHub Actions for the repo which can be done on the repo website page under the Actions tab. This workflow requires the use of GitHub secrets which can be set under repo settings. 

For more information on secrets, here is a [GitHub article on using secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions). 

For more information on CI/CD workflows, here is a [Databricks article on running a CI/CD workflow with a Databricks Asset Bundle and GitHub Actions](https://docs.databricks.com/en/dev-tools/bundles/ci-cd.html).

The secrets that need to be set for this repo will be:
  - `SP_TOKEN`: Service Principal access token, can also be a Personal Access Token (PAT) of a non-service principal.
  - `DATABRICKS_HOST`: same host url as above when setting up the Databricks CLI
  - `DATABRICKS_CLUSTER_ID`: a cluster that can be used to run this job.

Be aware that the token's user must have permissions to use the cluster in `DATABRICKS_CLUSTER_ID` and permissions to the catalog used above.



