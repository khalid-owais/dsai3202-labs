# Lab 3 – Data Preprocessing in Azure

## Amazon Electronics Reviews (Bronze → Silver → Gold)

### Student Info

**Name:** Owais Khalid
**Student ID:** 60306117


---

## Objective

In this lab, I used Azure Databricks to clean and transform the Amazon Electronics review dataset from Lab 2.
The goal was to follow a Bronze → Silver → Gold structure and create a final curated dataset ready for analytics or machine learning.

---

## Setup

I used:

* Azure Databricks
* Apache Spark (DataFrames)
* ADLS Gen2 storage
* Parquet format

The data from Lab 2 (partitioned by `review_year`) was used as the starting point.

---

## Notebook 1 – Load and Clean Reviews

First, I connected Spark to the storage account and loaded the partitioned Parquet data from the `processed` container.

I used:

```python
spark.read.option("basePath", base_path).parquet(f"{base_path}review_year=*/")
```

This allowed Spark to correctly read all partitions.

Then I applied basic cleaning:

* Removed rows where `asin`, `reviewerID`, or `overall` were null
* Ensured ratings were between 1 and 5
* Trimmed whitespace from `reviewText`
* Removed very short reviews

The cleaned data was saved to:

```
processed/clean_reviews/
```

---

## Notebook 2 – Enrich with Metadata

In this notebook, I joined the cleaned reviews with the metadata file from the `raw` container.

I selected only useful metadata fields:

* asin
* title
* brand
* price

I used a **left join** on `asin` so no reviews were lost if metadata was missing.

The result was saved to:

```
processed/enriched_reviews/
```

---

## Notebook 3 – Create Gold Dataset

Finally, I selected the final feature set:

* asin
* title
* brand
* price
* reviewerID
* overall
* summary
* reviewText
* helpful
* reviewTime
* review_year

This dataset was written to the `curated` container:

```
curated/features_v1/
```

I verified the output by checking the row count and previewing a few records.

---

## Databricks Job

I created a job with three tasks:

1. Load and clean reviews
2. Enrich with metadata
3. Write gold dataset

Each task depended on the previous one to ensure correct execution order.

---

## Issues Faced

* Storage key error (fixed by regenerating key)
* Partition reading confusion (fixed using `basePath`)
* Large dataset took time to process

After fixing these, the job ran successfully.

---

## Final Result

The Gold dataset (`features_v1`) contains cleaned and enriched review data organized for analytics and future ML tasks.

This completes the Bronze → Silver → Gold pipeline for Lab 3.