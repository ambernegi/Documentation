# RAG Overview

Retrieval-Augmented Generation (RAG) is a powerful AI framework that grounds large language models (LLMs) with external sources, making their responses more accurate and up-to-date.

## Why RAG?

LLMs are great at understanding language and context, but they have limitations:

- **Hallucination:** LLMs can confidently generate incorrect information.
- **Limited by Training Data:** They know nothing outside their training data.
- **Black Box Outputs:** It's hard to trace why a particular answer was generated.

## RAG to the Rescue

RAG helps solve these problems by:

- **Grounding answers in external knowledge** (like PDFs, databases, or websites)
- **Retrieving relevant information** for each user query
- **Providing context to the LLM** so it can generate more reliable, traceable answers

---

In the following steps, you'll see how to build a RAG pipeline using Langchain and Ollama.
