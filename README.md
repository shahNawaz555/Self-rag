Self-RAG: Retrieval-Augmented Generation with Self-Reflection
This project implements a Self-RAG (Retrieval-Augmented Generation with Self-Reflection) system in Python, designed to generate accurate and grounded answers to user questions by retrieving relevant documents, grading their relevance, and ensuring the generated responses are factual and address the query. Built using LangChain, LangGraph, and a modular workflow, it incorporates self-reflection mechanisms to improve query formulation and response quality. The system is optimized for questions about agent memory and prompt engineering, using a vector store for document retrieval.
Features

Document Retrieval:
Fetches relevant documents from a Chroma vector store using HuggingFace embeddings.
Splits web documents into chunks for efficient retrieval.


Relevance Grading:
Grades retrieved documents for relevance to the user’s question using a structured LLM.


Self-Reflection:
Rewrites queries to improve retrieval if documents are irrelevant.
Checks for hallucinations and ensures responses address the question.


Modular Workflow:
Uses LangGraph to manage a stateful graph with nodes for retrieval, grading, generation, and query transformation.


LLM Integration:
Powered by Grok (via Groq API) for generation and grading tasks.


Tracing:
Integrates with LangSmith for debugging and tracing.



Requirements

Python 3.8+
Dependencies (install via pip):pip install langchain langchain-community langchain-groq langchain-huggingface langgraph chromadb python-dotenv tiktoken


API Keys (set as environment variables):
Groq API (GROQ_API_KEY) for LLM.
LangChain API (LANGCHAIN_API_KEY) for tracing.


Environment Variables:export LANGCHAIN_TRACING_V2="true"
export LANGCHAIN_ENDPOINT="https://api.smith.langchain.com"



Installation

Clone the repository:
git clone https://github.com/your-username/self-rag.git
cd self-rag


Install dependencies:
pip install -r requirements.txt


Set up environment variables in a .env file:
GROQ_API_KEY=your-groq-api-key
LANGCHAIN_API_KEY=your-langchain-api-key


Load environment variables:
from dotenv import load_dotenv
load_dotenv()



Usage
Run the main script to generate answers to questions about agent memory or prompt engineering:
python main.py

Example Input
from app import app

# Example 1: Agent memory
inputs = {"question": "Explain how the different types of agent memory work?"}
for output in app.stream(inputs):
    for key, value in output.items():
        print(f"Node '{key}':")
    print("\n---\n")
print("Final Answer:", value["generation"])

# Example 2: Prompt engineering
inputs = {"question": "Explain how chain of thought prompting works?"}
for output in app.stream(inputs):
    for key, value in output.items():
        print(f"Node '{key}':")
    print("\n---\n")
print("Final Answer:", value["generation"])

Output

Console Output: Displays the workflow steps (retrieval, grading, generation, etc.) and the final answer.
Trace Logs: Available via LangSmith for debugging.

Project Structure
self-rag/
├── main.py                 # Main script to run the Self-RAG system
├── app.py                  # LangGraph workflow definition
├── requirements.txt        # Python dependencies
├── .env                    # Environment variables (not tracked)
└── README.md               # This file

How It Works

Vector Store Setup:

Loads documents from specified URLs (e.g., Lilian Weng’s blog posts on agents and prompt engineering).
Splits documents into chunks using RecursiveCharacterTextSplitter.
Embeds chunks with HuggingFace’s all-MiniLM-L6-v2 model and stores them in a Chroma vector store.


LangGraph Workflow:

Retrieve Node: Fetches relevant documents using the vector store retriever.
Grade Documents Node: Filters documents for relevance using a Grok-based grader.
Transform Query Node: Rewrites the question if no relevant documents are found.
Generate Node: Produces an answer using a RAG chain, grounded in retrieved documents.
Grading Edges:
Checks for hallucinations (ensures the answer is fact-based).
Verifies the answer addresses the question.




State Management:

Tracks the question, retrieved documents, and generated answer in a GraphState dictionary.
Ensures seamless transitions between nodes.


LLM and Embeddings:

Uses Grok (Gemma2-9b-It) for generation, grading, and query rewriting.
Employs HuggingFace embeddings for document retrieval.



Example Workflow
For the question: "Explain how chain of thought prompting works?"

Retrieve: Fetches documents related to prompt engineering.
Grade Documents: Filters out irrelevant documents.
Generate: Produces an answer based on relevant documents.
Grade Generation:
Checks if the answer is grounded (no hallucinations).
Verifies if the answer resolves the question.


Transform Query (if needed): Rewrites the question if documents or answers are insufficient.
