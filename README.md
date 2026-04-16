# Newsphere

An interactive 3D semantic map of 7,000 news articles from 2018, built as an MSc project in unsupervised NLP. No labels were provided at any stage — the topic clusters emerge entirely from the geometry of sentence embeddings.

**[Open the visualization](https://rahulrayy.github.io/Corpus-Map/index.html)** · **[Read the write-up](https://rahulrayy.github.io/Corpus-Map/writeup.html)**

---

## What it does

Each article is represented as a point in a 3D space where proximity reflects semantic similarity. Articles about the same topic cluster together naturally. The interface lets you:

- Rotate, zoom, and pan the 3D embedding space
- Hover over any point to see the headline, date, and source
- Click a point or a legend item to highlight a topic cluster
- Browse all articles in a cluster via the sidebar
- Filter articles by publication date using the timeline scrubber

---

## How it works

The full pipeline runs offline in Python and exports a single static JSON file. The frontend loads that file and renders everything client-side — no backend, no API, no database.

```
Raw articles (CC-News, 2018)
        ↓
Sentence embedding — all-MiniLM-L6-v2 (384 dims)
        ↓
Dimensionality reduction — UMAP 3D (cosine distance)
        ↓
Clustering — HDBSCAN
        ↓
Auto-labelling — TF-IDF per cluster
        ↓
Export — news_map.json
        ↓
Visualization — Three.js (GitHub Pages)
```

The entire Python pipeline is contained in a single notebook, `Corpus Map.ipynb`, designed to run on Google Colab with Google Drive mounted. Intermediate outputs (embeddings, UMAP coordinates, cluster labels) are saved to Drive at each stage so the notebook can be resumed without recomputing from scratch.

---

## Results

| Method | Trustworthiness | DBCV |
|---|---|---|
| UMAP 3D + HDBSCAN (this project) | 0.9065 | 0.1994 |
| t-SNE 2D + HDBSCAN | 0.8727 | n/a |
| UMAP 2D + HDBSCAN | 0.8511 | n/a |

27 clusters were identified from 7,000 articles. Selected clusters include:

| Cluster | Label | Size |
|---|---|---|
| 01 | facebook, cambridge, analytica | 84 |
| 02 | korea, north korea, korean | 61 |
| 03 | sunderland, cup, world cup | 185 |
| 13 | crash, driving, self driving | 101 |
| 14 | china, tariffs, news | 95 |
| 26 | infinity, infinity war, netflix | 108 |

---

## Repo structure

```
Corpus-Map/
├── index.html                  ← 3D visualization (Three.js)
├── writeup.html                ← project write-up
├── Corpus Map.ipynb            ← full Python pipeline (Colab)
└── data/
    └── news_map.json           ← pre-computed embeddings + cluster assignments
```

---

## Running the pipeline yourself

The notebook is designed to run on Google Colab. Open `Corpus Map.ipynb`, mount your Google Drive when prompted, and run all cells in order. Install dependencies with:

```bash
pip install sentence-transformers umap-learn hdbscan scikit-learn datasets
```

The full pipeline from raw data to exported JSON takes approximately 30 minutes on a standard Colab CPU instance. All intermediate files are saved to `MyDrive/news_explorer/` so you can resume from any stage without starting over.

---

## Stack

- **Embedding** — [sentence-transformers](https://www.sbert.net/) / all-MiniLM-L6-v2
- **Dimensionality reduction** — [UMAP](https://umap-learn.readthedocs.io/)
- **Clustering** — [HDBSCAN](https://hdbscan.readthedocs.io/)
- **Visualization** — [Three.js](https://threejs.org/)
- **Data** — [CC-News](https://huggingface.co/datasets/cc_news) via HuggingFace

---

## Note on links

Articles are from 2018. Some links may no longer be active as publishers update or remove old content.

---

## References

- Reimers, N. and Gurevych, I. (2019). Sentence-BERT: sentence embeddings using siamese BERT networks. *EMNLP 2019*.
- McInnes, L., Healy, J. and Melville, J. (2018). UMAP: uniform manifold approximation and projection for dimension reduction. *arXiv:1802.03426*.
- McInnes, L., Healy, J. and Astels, S. (2017). HDBSCAN: hierarchical density based clustering. *Journal of Open Source Software*.
- McInnes, L. and Healy, J. (2017). Density-based clustering validation. *SDM 2017*.
