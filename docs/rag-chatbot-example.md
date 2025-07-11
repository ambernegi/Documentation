# RAG Chatbot Example

Let's put it all together and build a simple chatbot interface using Gradio.

```python
import gradio as gr

def respond(question, history):
    return qa(question)["result"]

gr.ChatInterface(
    respond,
    chatbot=gr.Chatbot(height=500),
    textbox=gr.Textbox(placeholder="Ask me question related to Plants and their diseases", container=False, scale=7),
    title="Plant's Chatbot",
    examples=["What are different kinds of plant diseases", "What is Stewart’s wilt disease"],
    cache_examples=True,
    retry_btn=None,
).launch(share=True)
```

> This launches an interactive chatbot that uses your RAG pipeline to answer questions based on your documents. 