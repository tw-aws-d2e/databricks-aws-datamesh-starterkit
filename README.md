# stock_last_sales

The 'stock_last_sales' project is an example project to show how to implement a data product with 
Databricks Asset Bundles.

Article: 

https://www.datamesh-architecture.com/howto/build-a-dataproduct-with-databricks

## Steps 
### Notes:
To fully run this starter kit, you need permissions for:
  - Creating job clusters, catalogs, and personal access tokens on Databricks workspace
  - Creating Data Mesh Manager API Key

### 1. In this article, we will use Data Contracts and the new Databricks Asset Bundles that are a great fit to implement data products. All source code of this example project is available on GitHub.(https://github.com/badrishdavey/databricks-dataproduct-example)


### 2. To set up a local Python environment, we can use venv and install the development dependencies:

```
python3.10 -m venv .venv
source .venv/bin/activate
pip install -r requirements-dev.txt
```

### 3. Follow steps to [set up Databricks CLI](https://docs.databricks.com/en/dev-tools/cli/tutorial.html).

### 4. Set up secrets for the Data Mesh Manager
  1. On the Data Mesh Manager website, click your username in the top right corner.
  2. Go to the API Keys tab on the left side.
  3. Add API key, give it a descriptive name and copy the key.
  4. In the CLI type the following commands, hit enter after each one. It will prompt you to add the API key after the `put-secret` command.

```
databricks secrets create-scope datamesh_manager
databricks secrets put-secret datamesh_manager api_key
```

### 5. Replace workspace urls
Replace the host urls and email notification settings with your own workspace url and email in `databricks.yml`, `dataproduct.yml`, and `datacontract.yml`.

### 6. Change catalog 
If you do not have permissions to the catalog listed in `datacontract.yml` under `servers: > catalog:`, change the name of the catalog and replace all instances in the other files:
  - `dataproduct.yml`
  - `/src/create_unity_catalog.ipynb`
  - `/src/prepare.ipynb`
  - `/src/stock_last_sales/main.py`

### 7. When we are confident, we can deploy the bundle to our Databricks dev instances (manually for now):

```
databricks bundle deploy
```

### 8. And let's trigger a manual run of our workflow:
```
databricks bundle run data_ingestion_job
databricks bundle run stock_last_sales_job
```
You should see the job appear in your Databricks workspace. When `stock_last_sales_job` is finished, you will be able to see it registered in the Data Mesh Manager.

### 9. Setting up GitHub Actions:
Using GitHub Actions, we can automate deployment of the bundle every time there is a push. You must turn on GitHub Actions for the repo which can be done on the repo website page under the Actions tab. This workflow requires the use of GitHub secrets which can be set under repo settings. 

For more information on secrets, here is a [GitHub article on using secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions). 

For more information on CI/CD workflows, here is a [Databricks article on running a CI/CD workflow with a Databricks Asset Bundle and GitHub Actions](https://docs.databricks.com/en/dev-tools/bundles/ci-cd.html).

The secrets that need to be set for this repo will be:
  - `SP_TOKEN`: Service Principal access token, can also be a Personal Access Token (PAT) of a non-service principal.
  - `DATABRICKS_HOST`: same host url as above when setting up the Databricks CLI
  - `DATABRICKS_CLUSTER_ID`: a cluster that can be used to run this job.

Be aware that the token's user must have permissions to use the cluster in `DATABRICKS_CLUSTER_ID` and permissions to the catalog used above.



