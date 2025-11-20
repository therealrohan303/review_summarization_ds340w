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

## Downloading and Opening the notebook in Google Colab

You only need to do this once per machine.

- Download the DS340W FINAL CODE.ipynb file from this github.
- Go to https://colab.research.google.com/
- Upload the downloaded notebook
- Optional but recommended: In Colab, change runtime type and set Hardware accelerator to GPU, then save it.

From now on you should be able to reopen the notebook from recents in Colab, instead of uploading again.

## Notebook overview

The notebook is generally organized in the following blocks, each with its own heading:
- Dataset setup
- Data and Repo
- Building gold summaries and input review texts
- Reproduction
  - ROUGE
  - Polarity
- Enhancement 1. Sentence level evidence attribution
- Enhancement 2. Pros and cons coverage
- Enhancement 3. Coverage and evidence aware PoCa
- Extras

There are some instructions and/or general details in the notebook about the code as well to guide you.

## How to run the notebook from top to bottom

Run the notebook in this order. Do not skip ahead.

1. Mount Drive and set paths
- In the "Dataset setup" section, run the first cell that imports libraries and mounts Google Drive.
- When Colab asks for permission, follow the link, pick your Google account, copy the code, and paste it back.
- Make sure the DATA_ZIP_PATH in that cell points to your AmaSum zip (Default used by us: /content/drive/MyDrive/AmaSum/raw_min_10_max_100_revs.zip)
- Leave SPLIT = "test" for the main full run.

2. Data and Repo
- Run the cells under Data and Repo:
  - Create a local data folder
  - Unzip raw_min_10_max_100_revs.zip into it
  - Clone the PoCa repo into /content/polarity_calibration_naacl_2024

3. Build gold summaries and input review texts
- Run the cells under Building gold summaries and input review texts:
  - Read the AmaSum JSON files for the chosen split
  - Write one text file per product for the gold summary (verdict plus pros plus cons)
  - Write one text file per product that joins all customer reviews

4. Reproduction section
- Run the ROUGE cell first. It reads the released base and PoCa predictions from the cloned PoCa repo and computes ROUGE 1, 2, L, and Lsum against the gold summaries.
- Then run the polarity cell. It loads a sentiment model and computes polarity RMSE and MAE between input reviews and summaries.
- Finally, there is an optional "gen_demo_base" cell that generates a few example base summaries using flan t5.

5. Enhancement 1
- Run the cell that loads the sentence embedding model and the NLI model.
- Then run the big cell that builds evidence files. This loops over every product, retrieves similar review sentences for each summary sentence, checks entailment, and writes JSONL evidence files for both base and PoCa.
- Run the cell that reads those JSONL files and prints the attribution table, and the small example cell that prints one product with its summary and evidence.

6. Enhancement 2
- Run the cells that:
  - Use the same sentiment model to mark each summary sentence as pro, con, or neutral
  - Compare the share of pros and cons in the system summaries to the gold pros and cons
  - Run all cells in order.
  - There is a block in Enhancement 2 that tunes the pros and cons thresholds using the validation split by calling it directly. You do not need to change SPLIT for this. After tuning, we keep those thresholds fixed and run the main coverage table on the test split.

7. Enhancement 3
- Make sure Enhancement 1 and Enhancement 2 have already been run for the test split.
- Run the cells in this section...
  - Build a new PoCaCov summary by dropping unsupported PoCa sentences and optionally adding one missing pro or con
  - Recompute ROUGE and polarity numbers for the new system
  - Recompute the coverage table with base, PoCa, and PoCaCov.

8. Extras
- Run the last cells if you want the extra aspect bucket heatmap.
- This uses simple aspect keyword lists and the existing summaries and gold text, so it runs fairly fast.

## Important runtime warnings

Some parts of the notebook are heavy and can take a long time on a free Colab runtime, even with a GPU. 

1. Reproduction polarity cell
- Uses a large sentiment model over the whole test split.
- Expect this to be slow. Let it finish once rather than stopping and restarting it.
- Can take up to 2 hours if on CPU, and evenon GPU, an hour at least.

2. Reproduction gen_demo_base cell
- This can take a long time, since it runs beam search on several examples.
- Up to 30-40 minutes approx.

3. Enhancement 1 evidence building cell
- This is one of the slowest parts of the whole project too
- It does retrieval and NLI for every summary sentence for both base and PoCa.
- Can easily run for up to an hour.
- Please start it and let it run until it finishes. Stopping it early can leave partial JSONL files and break later steps.

4. Enhancement 2 threshold tuning on validation
- Can take about 40 minutes if not more.

5. Enhancement 3 polarity recomputation
- Reuses the sentiment model on the new PoCaCov summaries, so it is also slow...will take about an hour.

If Colab disconnects or your runtime dies, you will need to:
- Reconnect the runtime.
- Rerun the setup and data sections at the top.
- Rerun any long sections whose outputs were not written to disk.
- The evidence JSONL files and CSV tables are saved under polarity_calibration_naacl_2024/artifacts. If those files already exist and are complete, the notebook will reuse them rather than recomputing.




