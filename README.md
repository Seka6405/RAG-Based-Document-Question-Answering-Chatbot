# RAG-Based Document Query Chatbot using FAISS and LLMs

A Python application that lets users upload PDF or text documents and ask natural-language questions about their content. It uses **Retrieval-Augmented Generation (RAG)** to retrieve relevant text with vector similarity search and send that context to a large language model (LLM) for answer generation.

The project includes a lightweight Streamlit interface, a FAISS-based retrieval pipeline, Sentence Transformers embeddings, and Groq-hosted LLM inference.

## Features

- **PDF and TXT upload:** Load a document through the Streamlit interface.
- **Text extraction:** Extract text from PDFs using PyMuPDF and read text files.
- **Semantic embeddings:** Encode document text and queries with `all-MiniLM-L6-v2` from Sentence Transformers.
- **Vector similarity search:** Index embeddings with FAISS and retrieve the top three matching text lines for a query.
- **LLM-generated answers:** Send the retrieved context and question to a Groq-hosted Llama model.
- **Simple UI:** Ask questions and view answers directly in Streamlit.
- **Additional agentic prototype:** `agentic_rag.py` contains an alternate LangChain-based interface with document retrieval, conversation memory, model selection, and DuckDuckGo search commands.

## How RAG Works

```text
Upload PDF or TXT
       |
       v
Extract document text
       |
       v
Create embeddings with Sentence Transformers
       |
       v
Build a FAISS vector index
       |
       v
Embed the user's question
       |
       v
Retrieve top-k similar text chunks
       |
       v
Pass retrieved context + question to Groq LLM
       |
       v
Display generated answer in Streamlit
```

## Tech Stack

| Component | Technology |
|---|---|
| Language | Python |
| User interface | Streamlit |
| RAG / retrieval | Custom Python pipeline |
| Vector search | FAISS (`faiss-cpu`) |
| Embeddings | Sentence Transformers, `all-MiniLM-L6-v2` |
| PDF extraction | PyMuPDF (`fitz`) |
| LLM inference | Groq API, Llama model |
| Supporting libraries | NumPy, python-dotenv |
| Alternate prototype | LangChain, Hugging Face embeddings, DuckDuckGo search |

## Project Structure

```text
.
├── rag_ui.py          # Streamlit upload and question-answering UI
├── rag_pipeline.py    # Text extraction, embeddings, FAISS retrieval, LLM call
├── agentic_rag.py     # Alternate LangChain RAG + browser-search prototype
├── requirements.txt   # Python dependencies
└── documents/         # Optional PDF folder used by agentic_rag.py
```

## Getting Started

### 1. Clone the repository

Replace the placeholder with your actual GitHub repository URL.

```bash
git clone https://github.com/<your-username>/<your-repository>.git
cd <your-repository>
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

**Windows PowerShell**
```powershell
.venv\Scripts\Activate.ps1
```

**macOS / Linux**
```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

The project imports PyMuPDF as `fitz` and uses `python-dotenv` for environment variables. If installation fails, ensure the dependency file uses package names such as `PyMuPDF` and `python-dotenv`, rather than only their import names.

### 4. Configure your Groq API key

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
```

Keep `.env` out of version control. Add this to `.gitignore`:

```gitignore
.env
.venv/
__pycache__/
*.py[cod]
```

In `rag_pipeline.py`, read the key with `os.getenv("GROQ_API_KEY")`. Do not place API keys directly in Python source code or commit them to GitHub. Review `agentic_rag.py` as well before using it; its current key lookup should be updated to use the same environment-variable pattern.

### 5. Run the document chatbot

```bash
streamlit run rag_ui.py
```

Streamlit will print a local URL, commonly `http://localhost:8501`. Open it in your browser, upload a PDF or TXT file, and enter a question about the document.

## Example Usage

1. Start the app.
2. Upload a PDF or text file.
3. Enter a question grounded in the uploaded document, such as:
   - “Summarize the main points.”
   - “What methods does the document describe?”
   - “Which limitations are mentioned?”
4. Review the generated answer.

The current implementation retrieves up to three similar text lines. Answer quality depends on the document text, retrieval results, model behavior, and prompt context.

## Implementation Notes

### `rag_pipeline.py`

- Extracts PDF text using PyMuPDF or reads text from a TXT upload.
- Embeds extracted text with Sentence Transformers.
- Builds a FAISS `IndexFlatL2` index.
- Retrieves the three nearest items for each query.
- Sends retrieved context and the question to the Groq chat-completion API.

### `rag_ui.py`

- Provides the Streamlit file uploader.
- Builds the RAG pipeline for the uploaded document.
- Accepts a natural-language question and displays the response.

### `agentic_rag.py`

This file is an alternate prototype using LangChain components. It contains model selection, conversation memory, PDF loading from a local `documents/` directory, retrieval with source-document output, and a `search:` command backed by DuckDuckGo. It has a different setup from the upload-based app and may require code fixes and additional dependencies before running.

## Current Limitations and Potential Improvements

- **Chunking:** The basic pipeline splits extracted text by line, which may create very short or incomplete context. Add configurable overlapping text chunks.
- **Retrieval quality:** Tune `top_k`, use a suitable distance metric, and evaluate retrieval relevance.
- **Source references:** Return document/page metadata with retrieved passages to make answers easier to verify.
- **Prompt grounding:** Add instructions to say when the supplied context does not contain the answer.
- **Performance:** Cache embedding models and avoid rebuilding the vector index unnecessarily.
- **Error handling:** Validate empty documents, extraction failures, API errors, and missing API keys.
- **Security:** Keep secrets in environment variables and avoid uploading sensitive documents to external services without appropriate consent.
- **Testing:** Add tests for PDF/TXT extraction, retrieval behavior, and API failure cases.

## Security Notice

The source files provided for this project appear to use a hard-coded-looking string as the argument to `os.getenv(...)` instead of the environment-variable name `GROQ_API_KEY`. Before publishing, change the code to read the key from the environment and inspect your repository history for any real credentials. If an actual API key was exposed, revoke it and create a replacement.

## Author

**Sekhar Kolluru**

- GitHub: [@Seka6405](https://github.com/Seka6405)
- Email: [sekharkolluru06@gmail.com](mailto:sekharkolluru06@gmail.com)

---

If you find this project useful, feel free to explore the repository and share feedback.
