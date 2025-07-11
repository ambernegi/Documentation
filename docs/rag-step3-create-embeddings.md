# Step 3: Create Embeddings

For each text chunk, we create embeddings—numerical representations that capture semantic meaning. These are stored in a vector database for efficient search.

```python
from langchain_community.embeddings import HuggingFaceEmbeddings
from langchain_community.vectorstores import FAISS

embedder = HuggingFaceEmbeddings()
vector = FAISS.from_documents(documents, embedder)
```

> Embeddings allow us to find the most relevant chunks for any user query.
