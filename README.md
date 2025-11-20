# DS 340W Final Project

## What this repo is

This repo has our final Colab notebook for DS 340W.
We reproduce the PoCa opinion summarization results on the AmaSum dataset using the released predictions from the parent repo, then add three simple enhancements on top.

Main file: DS340W_PRE_FINAL_CODE.ipynb final Colab notebook with all code, plots, and metrics

Everything is written so someone can run it from a clean machine using Google Colab.

## Requirements

You will need:
- A Google account
- Google Drive access
- Google Colab
- A stable internet connection, since several large models are downloaded the first time
- A GPU runtime in Colab is strongly recommended
- In Colab. Runtime → Change runtime type → set Hardware accelerator to GPU
- The notebook also installs a lot of required Python packages it needs inside Colab.

## Downloading the AmaSum dataset and putting it in Google Drive

We use the AmaSum dataset from the SelSum repo...the version with at most 100 reviews per product.

- Go to the SelSum data page: https://github.com/abrazinskas/SelSum/tree/master/data
- Download the file raw_min_10_max_100_revs.zip from that page (This: JSON	374.8 MB	10	76	100)
- Do not unzip it on your machine.
- Open Google Drive in your browser.
- In My Drive, create a new folder called "AmaSum"
- Upload raw_min_10_max_100_revs.zip into this AmaSum folder.
- After this, the file should be at this path inside Drive: My Drive / AmaSum / raw_min_10_max_100_revs.zip
- The notebook expects exactly this path by default.
Note: If you want to put the file somewhere else in Drive or if you rename it, you will need to update the DATA_ZIP_PATH variable near the top of the notebook to match your own path.
