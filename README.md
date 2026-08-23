# YouTube RAG Assistant

A lightweight RAG-based chatbot that turns YouTube video transcripts into searchable knowledge and answers user questions with context from the video.

## Project Overview

YouTube RAG Assistant is an AI-powered conversational system that ingests a YouTube video transcript, processes it into meaningful chunks, converts those chunks into embeddings, stores them in a vector database, and retrieves the most relevant passages when the user asks a question. The retrieved context is then passed to a large language model (LLM) to generate a grounded answer based on the video's content.

This project solves a practical problem: while YouTube videos are rich in information, their content is not easily searchable or conversational in the way a structured knowledge base is. RAG helps bridge that gap by combining retrieval with generative reasoning. Instead of asking an LLM to answer from memory alone, the system retrieves relevant transcript segments from the specific video and uses them as context for more accurate, grounded responses.

Retrieval-Augmented Generation is especially useful here because:

- The source content is long-form and domain-specific.
- User questions are often highly contextual.
- You want answers based on the actual video transcript, not generic model knowledge.
- It reduces hallucination by grounding responses in retrieved evidence.

## Key Features

- YouTube video URL input
- Transcript extraction using the YouTube transcript API
- Text preprocessing and normalization
- Recursive text chunking for long transcripts
- Embedding generation with an embedding model
- Vector storage with FAISS
- Semantic similarity search
- Context retrieval based on relevance
- LLM-powered answer generation
- Conversational question-answering workflow
- Prompt-based response grounding to answer only from retrieved transcript context

## System Architecture

The project follows a standard Retrieval-Augmented Generation pipeline:

```mermaid
flowchart LR
    A[YouTube URL] --> B[Transcript Extraction]
    B --> C[Text Processing]
    C --> D[Chunking]
    D --> E[Embeddings]
    E --> F[Vector Store: FAISS]
    F --> G[Similarity Search]
    G --> H[Retrieved Context]
    H --> I[LLM]
    I --> J[Final Answer]
```

## How RAG Works in This Project

This project implements a classic RAG flow:

1. Document / transcript ingestion  
   The system accepts a YouTube video ID or URL and fetches the transcript text.

2. Text chunking  
   The full transcript is split into smaller chunks using a recursive text splitter. This makes retrieval more efficient and helps keep each chunk semantically coherent.

3. Embeddings  
   Each chunk is converted into embeddings using an embedding model. These vectors represent the semantic meaning of the chunk.

4. Vector storage  
   The generated embeddings are stored in a FAISS vector store for fast similarity-based retrieval.

5. Query embedding  
   When the user asks a question, it is embedded using the same embedding model.

6. Similarity search  
   The query vector is compared against the stored document vectors to find the most relevant transcript chunks.

7. Context retrieval  
   The top matching chunks are collected and used as the evidence set.

8. LLM response generation  
   The retrieved chunks are inserted into a prompt, and the LLM is asked to answer the question using only this context when possible.

## Tech Stack

| Component             | Technology                                       |
| --------------------- | ------------------------------------------------ |
| Programming Language  | Python                                           |
| Framework / Workflow  | LangChain                                        |
| LLM                   | OpenAI GPT model via LangChain                   |
| Embedding Model       | OpenAI text-embedding-3-small                    |
| Vector Store          | FAISS                                            |
| Transcript Extraction | youtube-transcript-api                           |
| Text Splitting        | langchain-text-splitters                         |
| Environment Variables | python-dotenv                                    |
| Token Management      | tiktoken                                         |
| Additional Libraries  | langchain, langchain-community, langchain-openai |

> The exact technologies used in this notebook are based on the implemented code. Where a technology is not explicitly present, it has been left as a placeholder rather than assumed.

## Project Structure

The current implementation is a notebook-based project and is organized around a single primary file:

```text
YouTube RAG Assistant/
├── YouTube RAG Assistant.ipynb
├── README.md
```

This repository, as implemented here, is centered on the notebook workflow for:

- transcript retrieval
- chunking
- embedding generation
- vector indexing
- retrieval
- prompt construction
- answer generation

## Installation & Setup

Follow these steps to set up the project locally.

### 1. Clone the repository

```bash
git clone <repository-url>
cd <repository-folder>
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

On Windows:

```bash
.venv\Scripts\activate
```

On macOS/Linux:

```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install --upgrade pip
pip install youtube-transcript-api langchain langchain-community langchain-openai langchain-text-splitters faiss-cpu tiktoken python-dotenv
```

### 4. Configure environment variables

Create a `.env` file in the project root and add your API key:

```bash
OPENAI_API_KEY=your_api_key_here
```

### 5. Run the notebook

Open the notebook in Jupyter or VS Code and run the cells in sequence:

- transcript fetching
- chunking
- embedding generation
- vector store creation
- retrieval
- answer generation

## Environment Variables

Example `.env` file:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

> Do not commit real API keys to GitHub. Keep them in local environment variables or a local `.env` file that is excluded from version control.

## Usage

1. Open the notebook.
2. Provide a valid YouTube video URL or video ID.
3. Retrieve the transcript.
4. Create chunks and store them in the vector store.
5. Ask a question about the video's content.
6. The retriever finds relevant transcript segments.
7. The LLM generates a final answer using the retrieved transcript context.

Example workflow:

- Input video: a YouTube lecture or technical explanation
- Ask: “What are the main concepts discussed in this video?”
- The system retrieves relevant transcript chunks
- The LLM responds based on those chunks

## Example

The following example is illustrative and demonstrates the intended behavior of the assistant:

User:

> "What are the main concepts explained in this video?"

Assistant:

> "The video primarily explains the core ideas behind retrieval-augmented generation, embeddings, vector search, and how an LLM can answer questions using transcript context from a specific source."

> This example is illustrative and intended to show the expected interaction style.

## RAG Components

This project uses the standard building blocks of a RAG pipeline:

- Document Loader  
  Extracts transcript content from a YouTube video.

- Text Splitter  
  Breaks long transcript text into smaller chunks for better indexing and retrieval. In this project, the notebook uses `RecursiveCharacterTextSplitter`.

- Embedding Model  
  Converts text chunks into numeric vectors for semantic similarity matching. In this project, `OpenAIEmbeddings` is used.

- Vector Store  
  Stores the embeddings and supports fast similarity search. This project uses `FAISS`.

- Retriever  
  Finds the most relevant chunks for a query using similarity-based search. The notebook creates a retriever with `vector_store.as_retriever(...)`.

- Prompt  
  Combines the retrieved context and the user question so the model answers from the relevant transcript content.

- LLM  
  Generates the final response. In this project, `ChatOpenAI` is used with a GPT model.

## Why RAG?

A general-purpose LLM may know broad information, but it does not inherently know the exact content of a particular YouTube video unless that content is provided as context. RAG is useful here because it allows the system to:

- retrieve the most relevant transcript passages
- ground the response in the actual video material
- reduce hallucination
- answer questions more accurately about specific videos

For a project centered on a single source document such as a transcript, RAG is a practical and reliable approach.

## Challenges & Solutions

Some real challenges in this workflow include:

- Long transcripts  
  Large transcripts can be difficult to process and search efficiently. This project addresses this by splitting the transcript into chunks before embedding and retrieval.

- Chunk size selection  
  If chunks are too large, they may contain too much irrelevant information. If too small, context may be fragmented. The notebook uses a recursive chunking strategy with a configured chunk size and overlap.

- Retrieval accuracy  
  Similarity search may return weak or partially relevant passages if the chunking or embedding setup is poor. A carefully chosen `k` parameter and transcript-aware prompt design help improve quality.

- Context limits  
  LLMs have finite context windows, so the retrieved passages must be concise and relevant. The project keeps retrieval focused on the top relevant chunks.

- Hallucination  
  Hallucination is reduced by instructing the model to answer only from the provided transcript context when possible.

- API limitations  
  YouTube transcript access may fail for some videos due to availability, restrictions, or transcript being disabled. The notebook includes error handling and explicit conditions for transcript retrieval.

## Future Improvements

These are realistic next steps and are not currently implemented in the existing notebook:

- Multi-video knowledge base support
- Conversation memory across turns
- Source citations and timestamps
- Better reranking of retrieved chunks
- Hybrid search combining keyword and semantic retrieval
- Streaming responses for a more interactive UI
- Multilingual transcript support
- Evaluation metrics for retrieval and answer quality
- Better frontend or web interface

## Learning Outcomes

This project demonstrates several important AI and ML concepts:

- RAG (Retrieval-Augmented Generation)
- LangChain orchestration
- Embedding generation
- Vector databases and vector search
- Semantic search
- LLM integration
- Prompt engineering
- Retrieval pipeline design
- Practical document QA over long-form text

## Performance / Evaluation

No specific quantitative evaluation metrics are provided in the current notebook, so the project does not claim measured performance numbers. However, the following metrics are relevant for evaluating this type of system:

- Retrieval accuracy
- Answer relevance
- Faithfulness to the source transcript
- Latency from query to answer
- Context precision
- User satisfaction with result quality

These metrics can be evaluated with a labeled test set of questions and answers grounded in the transcript.

## Screenshots / Demo

Add screenshots or a demo GIF here when available:

```md
![Application Screenshot](path/to/screenshot.png)
```

> Replace the placeholder path above with a valid image path when you add a screenshot or demo asset.

## Security & API Key Safety

This project uses API-based services and should handle secrets carefully:

- Store API keys in environment variables
- Never hardcode secrets in source files
- Do not commit `.env` files to GitHub
- Keep keys out of logs and notebooks unless explicitly required for local debugging

## Contributing

Contributions are welcome. To contribute:

1. Fork the repository.
2. Create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. Make your changes and keep the code readable.
4. Test the workflow carefully, especially transcript retrieval and answer generation.
5. Submit a pull request with a clear description of the change.

## License

This project does not currently declare a license in the repository. Please add a license before publishing publicly.

```md
License: [Add your license here]
```

## Author

Kartik

- GitHub: [[GitHub Profile URL](https://github.com/kartikmorya860-eng)]
- LinkedIn: [[LinkedIn Profile URL](https://www.linkedin.com/in/kartik-mourya-046b28308/)]

---

This project demonstrates a practical and portfolio-ready implementation of a YouTube transcript-based RAG system using LangChain, embeddings, FAISS, and an LLM. It is a strong example of how AI systems can retrieve and reason over domain-specific information from long-form video content.
