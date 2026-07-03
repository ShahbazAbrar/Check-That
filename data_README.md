# Dataset

The dataset is not committed to this repository. It must be downloaded separately from the official CheckThat! 2025 source.

## How to Get It

**Official source (CheckThat! 2025 Lab, Task 4b — Subtask B):**
https://gitlab.com/checkthat_lab/clef2025-checkthat-lab/-/tree/main/task4/subtask_4b

## What's in the Dataset

| File | Description | Size |
|---|---|---|
| `subtask4b_collection_data.pkl` | Corpus of 7,718 CORD-19 papers | ~25 MB |
| `subtask4b_query_tweets_train.tsv` | 12,853 training tweets (with gold labels) | ~2 MB |
| `subtask4b_query_tweets_dev.tsv` | 1,400 dev tweets (with gold labels) | ~0.3 MB |
| `subtask4b_query_tweets_test.tsv` | 1,446 test tweets (queries only) | ~0.3 MB |
| `subtask4b_query_tweets_test_gold.tsv` | 1,446 test tweets (with gold labels) | ~0.2 MB |

## Where to Place It

After download, extract the files to:

```
your_project_root/
└── data/
    └── raw/
        └── clef2025-checkthat-lab/
            └── task4/
                └── subtask_4b/
                    ├── subtask4b_collection_data.pkl
                    ├── subtask4b_query_tweets_train.tsv
                    ├── subtask4b_query_tweets_dev.tsv
                    ├── subtask4b_query_tweets_test.tsv
                    └── subtask4b_query_tweets_test_gold.tsv
```

Then update `PROJECT_ROOT` in the first cell of `IR.ipynb` to point to your project root.

## License

The dataset is licensed under **CC BY-NC 4.0** by the CheckThat! organizers. This means:
- You may use, share, and adapt it for non-commercial purposes
- You must give appropriate credit to the original creators
- See the license terms at: https://creativecommons.org/licenses/by-nc/4.0/

## Citation

If you use this dataset, please cite:

> CheckThat! Lab 2025 at CLEF, Task 4b: Scientific Claim Source Retrieval.
> https://checkthat.gitlab.io/clef2025/task4/

## Processed Files

Once you run the notebook, it generates processed files (Parquet, FAISS index, embeddings) under `data/processed/` and `data/embeddings/`. These are also not committed since they are derived artifacts — they will be regenerated automatically when you run the notebook.
