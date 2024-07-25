# stock_last_sales

The 'stock_last_sales' project is an example project to show how to implement a data product with 
Databricks Asset Bundles.

Article: 

https://www.datamesh-architecture.com/howto/build-a-dataproduct-with-databricks

### Steps 

#1. In this article, we will use Data Contracts and the new Databricks Asset Bundles that are a great fit to implement data products. All source code of this example project is available on GitHub.(https://github.com/badrishdavey/databricks-dataproduct-example)


#2. To set up a local Python environment, we can use venv and install the development dependencies:

```
python3.10 -m venv .venv
source .venv/bin/activate
pip install -r requirements-dev.txt
```

When we are confident, we can deploy the bundle to our Databricks dev instances (manually for now):

```
databricks bundle deploy
```

And let's trigger a manual run of our workflow:
```
databricks bundle run data_ingestion_job
databricks bundle run stock_last_sales_job
```


For that, we can use the Data Contract CLI tool to make this check:

```
export DATACONTRACT_DATABRICKS_HTTP_PATH=/sql/1.0/warehouses/18c2b1dd50f02017
export DATACONTRACT_DATABRICKS_TOKEN=dapib0a6edb7c6d21ee98c109d1ec763d540
datacontract test datacontract.yaml
```

