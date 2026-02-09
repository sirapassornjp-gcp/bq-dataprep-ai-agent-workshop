## Step 4: Create Dataset and Load Data

In this step, we will create a BigQuery dataset in the Bangkok region and load our raw data.

### 1. Create the BigQuery Dataset

1. In the Google Cloud Console, navigate to **BigQuery**.
2. In the **Explorer** pane on the left, locate your **Project ID**.
3. Click the **three vertical dots (View actions)** next to your Project ID and select **Create dataset**.
4. Fill in the dataset details:
   * **Dataset ID:** `bq_data_preparation_demo`
   * **Location type:** Select **Region**.
   * **Region:** Select `asia-southeast3 (Bangkok)`.
5. Leave all other settings as their defaults and click **Create dataset**.

### 2. Configure Query Location

Because our dataset is in Bangkok (`asia-southeast3`), we **must** configure the query engine to look in that region. If you skip this, you may get a "Not Found" error.

1. In the BigQuery Query Editor, click the **More** button > **Query settings**.
2. Scroll down to **Data location**.
3. Change the location from "Automatic location selection" to Region:  **`asia-southeast3 (Bangkok)`**.
4. Click **Save**.

### 3. Load the Tables via SQL

We will use SQL to load the tables. 
* For the third table (`err_dataprep`), we **manually define** the schema to ensure the nested JSON column is loaded correctly.

1. Click the **+ Compose new query** button.
2. Copy and paste the following SQL commands:

```sql
-- 1. Load the main product table
LOAD DATA OVERWRITE bq_data_preparation_demo.stg_product
FROM FILES (
  format = 'CSV',
  uris = ['gs://th-region-bq-ai-agent/stg_product.csv'],
  skip_leading_rows = 1
);

-- 2. Load the extended attributes table
LOAD DATA OVERWRITE bq_data_preparation_demo.stg_extended_product
FROM FILES (
  format = 'CSV',
  uris = ['gs://th-region-bq-ai-agent/stg_extended_product.csv'],
  skip_leading_rows = 1
);

-- 3. Load the error table (Manual Schema for JSON support)
LOAD DATA OVERWRITE bq_data_preparation_demo.err_dataprep
(
  row JSON,
  jobId STRING,
  nodeId STRING,
  stepId STRING,
  stepDescription STRING
)
FROM FILES (
  format = 'CSV',
  uris = ['gs://th-region-bq-ai-agent/err_dataprep.csv'],
  skip_leading_rows = 1
);
```

3. Click **Run**.

4. Verify that all three tables (stg_product, stg_extended_product, and err_dataprep) have appeared in your dataset.