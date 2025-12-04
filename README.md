# Load the news articles dataset into a DataFrame
import pandas as pd
articles_df = pd.read_csv("../data/Articles.csv", encoding="latin1")
articles_df.head()
# Preprocess text by lowercasing, removing punctuation, and tokenizing
import string
def preprocess(text):
    text = text.lower()
    text_no_punc = text.translate(str.maketrans('', '', string.punctuation))
    words = text_no_punc.split()
    return " ".join(words)
 # Remove English stopwords from each article to create cleaned documents
 processed_docs = []
for text in df["Article"]:
    sentence = text.split()         
    filtered_words = []
    
    for word in sentence:
        if word.lower() not in stoplist:
            filtered_words.append(word)
    
    processed_docs.append(" ".join(filtered_words))
print("original:", sentence)
print("processed:", filtered_words)
## Create TF-IDF vectors for all processed documents and view sample features
from sklearn.feature_extraction.text import TfidfVectorizer

vectorizer = TfidfVectorizer()
X = vectorizer.fit_transform(processed_docs)

print(X.shape)
print(vectorizer.get_feature_names_out()[:30])  

tfidf_df = pd.DataFrame(X.toarray(), columns=vectorizer.get_feature_names_out())
tfidf_df.head()
X.toarray()[:5]
# Convert TF-IDF matrix to a DataFrame and reload the original dataset for reference
tfidf_df = pd.DataFrame(X.toarray(), columns=v.get_feature_names_out())
tfidf_df.head()
df = pd.read_csv("../data/Articles.csv", encoding="latin1")
df.head()
# Perform TF-IDF + cosine similarity search to return the top_k most relevant articles
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

def search(query, top_k=5):
    """
    TF–IDF + cosine similarity search.
    Returns top_k most relevant articles.
    """
    clean_query = preprocess(query)
    query_vec = vectorizer.transform([clean_query]) 
    similarities = cosine_similarity(query_vec, X)[0]  
    top_indices = np.argsort(similarities)[::-1][:top_k]
    results = []
    for rank, idx in enumerate(top_indices, start=1):
        results.append({
            "doc_id": int(idx),  
            "rank": rank,
            "score": float(similarities[idx]),
            "heading": articles_df.loc[idx, "Heading"],
            "date": articles_df.loc[idx, "Date"],
            "category": articles_df.loc[idx, "NewsType"],
            "snippet": str(articles_df.loc[idx, "Article"])[:250].replace("\n", " ")
        })
    return results
df.columns
# Run a sample TF-IDF search query and print the top 5 ranked results
example_query = "stock market crash in asia"
results = search(example_query, top_k=5)
for r in results:
    print(f"Rank {r['rank']} | Score: {r['score']:.4f}")
    print(f"Heading : {r['heading']}")
    print(f"Date    : {r['date']} | Type: {r['category']}")
    print(f"Snippet : {r['snippet']}")
    print("-" * 90)
# Build the BM25 index by cleaning and tokenizing all articles
from rank_bm25 import BM25Okapi
import string

def remove_puncts(input_string):
    return input_string.translate(str.maketrans('', '', string.punctuation)).lower()

original_corpus = articles_df["Article"].astype(str).tolist()
cleaned_corpus = [remove_puncts(doc) for doc in original_corpus]
tokenized_corpus = [doc.split() for doc in cleaned_corpus]

bm25 = BM25Okapi(tokenized_corpus)
tokenized_corpus[:2]
# Perform BM25 search to return the top_k most relevant articles for a query
import numpy as np

def search_bm25(query, k=5):
    q_tokens = preprocess(query).split()
    scores = bm25.get_scores(q_tokens)
    top_idx = np.argsort(scores)[::-1][:k]

    results = []
    for i, idx in enumerate(top_idx):
        results.append({
            "doc_id": int(idx),  # evaluation
            "rank": i + 1,
            "score": float(scores[idx]),
            "heading": articles_df.loc[idx, "Heading"],
            "date": articles_df.loc[idx, "Date"],
            "category": articles_df.loc[idx, "NewsType"],
            "snippet": str(articles_df.loc[idx, "Article"])[:250].replace("\n", " ")
        })
    return results
# Run BM25 search for a sample query and display the top 5 results
example_query = "stock market crash in asia"
results_tfidf = search(example_query, top_k=5)
for r in results_tfidf:
    print(f"Rank {r['rank']} | Score: {r['score']:.4f}")
    print(f"Heading : {r['heading']}")
    print(f"Date    : {r['date']} | Type: {r['category']}")
    print(f"Snippet : {r['snippet']}")
    print("-" * 90)
# Run a sample BM25 search query and print the top 5 ranked results
results_bm25 = search_bm25(example_query, k=5)

for r in results_bm25:
    print(f"Rank {r['rank']} | BM25 Score: {r['score']:.4f}")
    print(f"Heading : {r['heading']}")
    print(f"Date    : {r['date']} | Type: {r['category']}")
    print(f"Snippet : {r['snippet']}")
    print("-" * 90)
search("stock market crash in asia", top_k=10)
search("covid vaccine rollout", top_k=10)
search("elections in pakistan", top_k=10)
# Evaluate TF-IDF retrieval performance using precision, recall, and F1-score for each query
ground_truth = {
    "stock market crash in asia": [126],
    "covid vaccine rollout": [],
    "elections in pakistan": []
}
from sklearn.metrics import precision_score, recall_score, f1_score
evaluation_results = {}
for query, relevant_docs in ground_truth.items():
    results = search(query, top_k=10)
    retrieved_docs = [r["doc_id"] for r in results]
    
    y_true = [1 if doc_id in relevant_docs else 0 for doc_id in retrieved_docs]
    y_pred = [1] * len(retrieved_docs)  
    
    precision = precision_score(y_true, y_pred, zero_division=0)
    recall = recall_score(y_true, y_pred, zero_division=0)
    f1 = f1_score(y_true, y_pred, zero_division=0)
    
    evaluation_results[query] = {
        "precision": precision,
        "recall": recall,
        "f1_score": f1
    }
evaluation_results