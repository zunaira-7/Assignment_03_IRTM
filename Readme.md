# Information Retrieval System – TF-IDF & BM25 Search Engine

## 📌 Overview
This project implements a classical Information Retrieval (IR) system capable of retrieving relevant news articles based on a user query. It explores essential IR concepts such as text preprocessing, indexing, vector-space representation, ranking models, and evaluation metrics.

The system supports:
- **TF–IDF + Cosine Similarity**
- **BM25 Ranking (BM25Okapi)**

---

## 📁 Dataset
The dataset consists of real-world news articles (CSV) with fields:
- Article Text
- Headline
- Date
- Category

---

## 🧱 System Architecture
### Components:
- **Dataset Loader** – Loads Articles.csv
- **Preprocessor** – lowercasing, punctuation removal, tokenization, stopword removal
- **TF–IDF Indexer** – builds sparse matrix using TfidfVectorizer
- **BM25 Indexer** – tokenizes text and applies BM25Okapi scoring
- **Search Module** – functions for TF–IDF and BM25 search
- **Evaluation Module** – calculates precision, recall, and F1-score

---

## 🛠 Methodology
### 1. Preprocessing
- Lowercase conversion  
- Punctuation removal  
- Tokenization  
- Stopword filtering  

### 2. TF–IDF Vectorization
- Builds vector-space model  
- Uses cosine similarity for search  

### 3. BM25 Ranking
- Probabilistic ranking algorithm  
- Considers term frequency saturation and document length  

### 4. Search Functions
Returns: document ID, score, headline, date, category, snippet.

### 5. Evaluation
Queries used:
- “stock market crash in asia”
- “covid vaccine rollout”
- “elections in pakistan”

Metrics: **Precision**, **Recall**, **F1-score**

---

## 📊 Evaluation Summary
- Only the first query had relevant documents in the dataset.  
- COVID-19 and election-related queries had **zero** relevant documents → dataset limitation.  
- TF–IDF successfully retrieved the only relevant document for the first query.  
- Demonstrates dependency of IR systems on dataset scope.

---

## 💬 Discussion
Key observations:
- Dataset quality heavily influences performance.
- TF–IDF retrieves semantically related content but lacks deep understanding.
- BM25 improves ranking but evaluation was limited.
- Preprocessing choices (no stemming/lemmatization) restrict retrieval precision.
- Small ground-truth sets limit metric depth.

---

## 🤖 AI Usage Disclosure
ChatGPT was used for:
- Explaining IR concepts  
- Reviewing code structure  
- Refining report sections  
- Suggesting README layout  

**All implementation work was completed independently.**

---

## 📚 References
YouTube videos on:
- TF–IDF  
- Cosine similarity  
- BM25  
- IR metrics  
(Links included in the original report.)

---

## 📌 Conclusion
The project successfully implemented a complete IR pipeline using classical models.  
It demonstrated:
- Effective retrieval when relevant documents exist  
- Dataset-driven performance limitations  
- Real-world IR challenges  

---

## 🔗 GitHub Repository
https://github.com/zunaira-7/Assignment_03_IRTM
