LLMs have a limited context window, so we split documents into smaller, meaningful chunks. This helps retrieve only the relevant information for each query.

=== "Python"
    ```python
    from langchain_experimental.text_splitter import SemanticChunker
    from langchain.embeddings import HuggingFaceEmbeddings

    text_splitter = SemanticChunker(HuggingFaceEmbeddings())
    documents = text_splitter.split_documents(docs)

    print("Number of chunks created: ", len(documents))
    ```

=== "Java"
    ```java
    // Example using hypothetical Java library for text splitting
    TextSplitter splitter = new SemanticChunker(new HuggingFaceEmbeddings());
    List`<Document>` documents = splitter.splitDocuments(docs);

    System.out.println("Number of chunks created: " + documents.size());
    ```
