# Retrieval-Augmented Generation (RAG) Assistant

A question-answering assistant that answers natural-language questions over a 200+ page policy report, grounding every answer in the source document to reduce hallucination. Built as a 6-stage RAG pipeline with a chat interface.

This was a group project (team of 3). I owned **Stage 5 (Generation)** and **Stage 6 (Interface)**.

## What it does

You ask a question in plain English, and the assistant retrieves the most relevant sections from the source document, then generates an answer that's grounded in — and cites — those sections. The interface also shows a pipeline trace, so you can see where each answer came from rather than trusting a black box.

The source document is the **2024 National Money Laundering Risk Assessment**, a structured policy report with sections such as Executive Summary, Introduction, Threats, Vulnerabilities and Risks, and Conclusion.

## The 6-stage pipeline

1. **Ingestion & Indexing** — load the PDF and parse it section-aware (using the document's heading structure) so chunks respect the report's real sections, then embed and store them in a Chroma vector index.
2. **Query Understanding** — interpret and prepare the user's question for retrieval.
3. **Retrieval** — fetch the most relevant chunks from the vector index.
4. **Context Refinement** — clean and refine the retrieved context before it reaches the model.
5. **Generation** *(my part)* — designed citation-enforced prompts so the model answers strictly from the retrieved context and cites its sources, reducing hallucination.
6. **Interface** *(my part)* — built a Gradio chat UI with a pipeline-trace view that shows which sources each answer was drawn from.

## Tech stack

Python · LangChain · OpenAI GPT-4o-mini · `text-embedding-3-small` · Chroma (vector store) · Gradio (UI)

## The source document

The source report is **not included** in this repository. To run the pipeline, obtain the 2024 National Money Laundering Risk Assessment (a publicly available U.S. Treasury report) and point the loader to the PDF.

> Note: the notebook was developed in Google Colab. The OpenAI API key is loaded securely from Colab secrets (`userdata.get('OPENAI_API_KEY')`) — no key is stored in the code.

## Running it

1. Install the dependencies:
   ```bash
   pip install langchain langchain-openai langchain-community langchain-chroma langchain-text-splitters chromadb gradio pypdf
   ```
2. Provide your OpenAI API key. In Colab, add it as a secret named `OPENAI_API_KEY`; locally, set it as an environment variable:
   ```bash
   export OPENAI_API_KEY="your-key-here"
   ```
3. Place the source PDF where the notebook expects it, then run the cells in order. The final stage launches the Gradio chat interface.

## What I took away from it

Working on the Generation and Interface stages showed me that a good RAG system is as much about *trust* as retrieval — enforcing citations in the prompt and surfacing the source trace in the UI made the answers far more reliable and verifiable than a plain chatbot. It's the difference between an answer you can check and one you just have to hope is right.
