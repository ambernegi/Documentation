# Step 5: Generation

With the relevant chunks retrieved, we pass them (along with the user query) to the LLM to generate a grounded, accurate answer.

=== "Python"
    ```python
    # Python code for generation
    # Example: Generate answer using LLM
    from langchain.chains import RetrievalQA
    qa = RetrievalQA.from_chain_type(llm, retriever=retriever)
    answer = qa.run(query)
    ```

=== "Java"
    ```java
    // Java code for generation (hypothetical example)
    // Example: Generate answer using LLM
    RetrievalQA qa = RetrievalQA.fromChainType(llm, retriever);
    String answer = qa.run(query);
    ```

> The LLM uses the retrieved context to answer accurately and transparently. 