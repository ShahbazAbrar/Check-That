# CheckThat! 2025 Task 4b — Hybrid Multilingual Scientific Claim Retrieval

An Information Retrieval pipeline that matches social media tweets making scientific claims to their underlying peer-reviewed source paper from a corpus of 7,718 COVID-19 research papers.

**Course:** Information Retrieval — Term Project
**Institution:** Air University Islamabad
**Group Members:**
- M. Abubakar Barket — 241410
- Shahbaz Abrar — 241454
- M. Ismaeel Khan — 241480

---

## Problem

During the COVID-19 pandemic, social media was flooded with health claims — some genuine, many false. Given a viral tweet making a scientific claim, our system returns the top-5 most relevant scientific papers from a fixed corpus, so fact-checkers can trace claims back to their original peer-reviewed source.

## Approach

A hybrid retrieval pipeline combining:

- **BM25** — classic keyword-based ranking (rank_bm25 library)
- **Dense retrieval** — semantic search using `multilingual-e5-large` embeddings + FAISS
- **Reciprocal Rank Fusion (RRF)** — combines both rankings into a single top-5
- **Sentence-level justification** — cross-encoder picks the most relevant abstract sentence for each retrieved paper
- **Out-of-domain filtering** — rejects irrelevant queries below a confidence threshold

## Results

| System | Split | MRR@1 | MRR@5 | Recall@5 |
|---|---|---|---|---|
| BM25 baseline | dev | 0.5857 | 0.6279 | 0.6986 |
| Dense (mE5-large) | dev | 0.5236 | 0.5899 | 0.6929 |
| **Hybrid RRF (main)** | **dev** | **0.6057** | **0.6627** | **0.7486** |
| **Hybrid RRF** | **test** | **0.4848** | **0.5475** | **0.6425** |

Official baseline: MRR@5 ≈ 0.43 — our system beats it by ~12 points on the test set.

## Dataset

- **Source:** CLEF 2025 CheckThat! Lab Task 4b ([official GitLab](https://gitlab.com/checkthat_lab/clef2025-checkthat-lab/-/tree/main/task4/subtask_4b))
- **License:** CC BY-NC 4.0
- **Size:** 7,718 papers, 12,853 train / 1,400 dev / 1,446 test tweets

## Repository Contents

| File | Purpose |
|---|---|
| `IR.ipynb` | Main notebook — full pipeline end-to-end |
| `app.py` | Streamlit demo application |
| `README.md` | This file |
| `requirements.txt` | Python dependencies |
| `report.pdf` | Short project report (2-4 pages) |
| `presentation.pdf` | Slide deck |

---

## How to Run

### Option 1: Google Colab (recommended)

1. Open `IR.ipynb` in Google Colab
2. Enable GPU: `Runtime → Change runtime type → T4 GPU`
3. Download the CheckThat! 2025 Task 4b dataset from the official GitLab
4. Upload the dataset to your Google Drive at a folder of your choice
5. In the first code cell, edit the `PROJECT_ROOT` path to point to your folder:
   ```python
   PROJECT_ROOT = '/content/drive/MyDrive/your_folder_here'
   ```
6. Run cells sequentially from top to bottom

### Option 2: Local Machine

**Requirements:**
- Python 3.10+
- 16 GB RAM
- NVIDIA GPU with 8 GB+ VRAM (T4 or better recommended)

**Steps:**

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/checkthat-2025-task4b.git
cd checkthat-2025-task4b

# 2. Install dependencies
pip install -r requirements.txt

# 3. Download the dataset from the CheckThat! GitLab and place it in ./data/

# 4. Edit PROJECT_ROOT in the notebook to a local path:
#    PROJECT_ROOT = './data'

# 5. Launch Jupyter and run the notebook
jupyter notebook IR.ipynb
```

### Running the Streamlit Demo

1. Sign up for a free ngrok account at https://ngrok.com
2. Get your auth token from https://dashboard.ngrok.com/get-started/your-authtoken
3. In the UI launch cell (Cell 22), replace `YOUR_NGROK_TOKEN_HERE` with your token
4. Run Cell 21 and Cell 22
5. Open the ngrok URL that appears

---

## Pipeline Architecture

```
Tweet input
    ↓
Cleaning (strip URLs, mentions, whitespace)
    ↓
    ├─→ BM25 keyword search        → top-100
    └─→ Dense mE5 + FAISS search    → top-100
    ↓
RRF fusion (combine into single ranking)
    ↓
Confidence check (top-1 score ≥ 0.82?)
    ↓
Top-5 papers + justification snippets
```

## Key Design Decisions

- **Why hybrid?** BM25 catches exact keyword matches; dense retrieval catches paraphrases and cross-lingual similarity. They make different mistakes, so combining them raises Recall@5 from 0.69 to 0.75.
- **Why RRF instead of score averaging?** BM25 scores are unbounded; dense cosine scores are in [-1, 1]. Direct combination requires fragile score normalization. RRF uses ranks only.
- **Why no cross-encoder reranking?** We tested three cross-encoder configurations and all underperformed hybrid RRF by 0.04-0.07 MRR@5. The tweet-to-title gap on this dataset is too small to benefit from reranking.
- **Why no credibility reranking?** Tested — hurt performance. Only ~10% of the corpus is in top-tier venues, so credibility weight pushes prestigious-but-irrelevant papers above genuinely-matched specialist papers.

## Limitations

- Single corpus, single domain (COVID-19). Results may not generalize.
- Multilingual evaluation uses synthetic NLLB translations, not real non-English tweets.
- No fine-tuning — all models used off-the-shelf.
- System retrieves evidence but does not decide claim truth automatically.

---

## Individual Contributions

- **M. Abubakar Barket (241410)** — Dataset preparation and preprocessing.
- **Shahbaz Abrar (241454)** — Core retrieval (BM25, dense mE5, hybrid RRF).
- **M. Ismaeel Khan (241480)** — Evaluation harness, Streamlit demo, presentation and report support.

## References

- CLEF 2025 CheckThat! Lab: https://checkthat.gitlab.io/clef2025/
- CORD-19 dataset: Wang et al., 2020 — "CORD-19: The COVID-19 Open Research Dataset"
- BM25: Robertson & Zaragoza, 2009 — "The Probabilistic Relevance Framework: BM25 and Beyond"
- mE5: Wang et al., 2024 — "Multilingual E5 Text Embeddings"
- FAISS: Johnson et al., 2019 — "Billion-scale similarity search with GPUs"

---

## License

Code released under MIT license. Dataset licensed under CC BY-NC 4.0 by the CheckThat! organizers.
