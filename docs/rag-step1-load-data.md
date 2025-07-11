# Step 1: Load Data

The first step in a RAG pipeline is to load your data. For this demo, we'll use a PDF about plant diseases, but you can use any relevant document.

=== "Python"
    ```python
    # Python code for loading data
    # Example: Load documents from a directory
    from langchain.document_loaders import DirectoryLoader
    loader = DirectoryLoader('data/')
    docs = loader.load()
    ```

=== "Java"
    ```java
    // Java code for loading data (hypothetical example)
    // Example: Load documents from a directory
    DirectoryLoader loader = new DirectoryLoader("data/");
    List<Document> docs = loader.load();
    ```

> This loads your PDF and lets you inspect its contents.
