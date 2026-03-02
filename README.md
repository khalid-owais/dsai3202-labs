
# Lab 2 – Data Ingestion in Azure

## Amazon Electronics Reviews

### Student Info

**Name:** 60306117
**Student ID:** 60306117
**Date:** 1 March 2026

---

## Objective

The goal of this lab was to upload the Amazon Electronics reviews dataset to Azure Blob Storage and use Azure Data Factory to convert the raw JSON file into partitioned Parquet format.

---

## Resources Created

* Storage Account: `amazondatalake60306117`
* Containers: `raw`, `processed`, `curated`
* Data Factory: `amazon-adf-60306117`
* Compute Instance: `amazon-compute-60306117`

---

## Data Upload

First, I downloaded the dataset from the Stanford SNAP website using `wget`.
After unzipping the file, I uploaded it to the `raw` container using `azcopy`.

The metadata file had an issue because it was written in Python dictionary format instead of valid JSON. I fixed this using a small Python script that converted each line into proper JSON and then uploaded the corrected file.

I verified the upload using:

```bash
az storage blob list --account-name amazondatalake60306117 --container-name raw --output table
```

The `raw` container now contains:

* `reviews_Electronics_5.json`
* `meta_Electronics.json.gz`
* `meta_Electronics_fixed.json`

---

## Azure Data Factory

I created a linked service connected to the storage account.

Then I created:

* A JSON dataset pointing to the raw reviews file
* A Parquet dataset pointing to the processed folder

In the Data Flow:

* Source: Raw JSON file
* Derived Column: Created `review_year` from `unixReviewTime`
* Sink: Parquet format, partitioned by `review_year`

Expression used:

```
review_year = year(toTimestamp(toLong(unixReviewTime) * 1000))
```

---

## Output

After running the pipeline, the processed container contains folders like:

```
processed/reviews/
review_year=2010/
review_year=2011/
review_year=2012/
review_year=2013/
review_year=2014/
```

Each folder contains Parquet files for that year.

---

## Issues Faced

* SAS token error due to using the wrong token at first
* Metadata file was not valid JSON
* Processing took time because the file is large

All issues were resolved and the pipeline ran successfully.

---

## Conclusion

The lab successfully ingested raw JSON data into Azure and transformed it into partitioned Parquet format using Azure Data Factory. The final output is stored in the processed container and organized by review year.

---
