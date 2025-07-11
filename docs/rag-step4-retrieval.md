# Step 4: Retrieval

Now, we can retrieve the most semantically similar text chunks from the vector store based on a user query.

=== "Python"
    ```python
    # Python code for retrieval
    # Example: Retrieve relevant chunks using a retriever
    retriever = FAISS.from_documents(documents, embeddings).as_retriever()
    relevant_docs = retriever.get_relevant_documents(query)
    ```

=== "Java"
    ```java
    // Java code for retrieval (hypothetical example)
    // Example: Retrieve relevant chunks using a retriever
    Retriever retriever = FAISS.fromDocuments(documents, embeddings).asRetriever();
    List<Document> relevantDocs = retriever.getRelevantDocuments(query);
    ```

> This step finds the best-matching information to ground the LLM's answer. 