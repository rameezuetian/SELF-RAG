# 🧠 SELF-RAG — Self-Reflective Retrieval-Augmented Generation

A step-by-step implementation of **Self-RAG (Self-Reflective Retrieval-Augmented Generation)** using **LangChain, LangGraph, OpenAI, FAISS, Pydantic, and Tavily**.

This project explores how a RAG system can become more reliable by allowing the LLM to **decide when retrieval is necessary, evaluate retrieved documents, verify generated answers against retrieved context, check answer usefulness, rewrite queries, and retry when the response is not satisfactory**.

The project is implemented progressively through multiple Jupyter notebooks, where each step adds a new self-reflection capability.

---

## 🚀 Project Overview

Traditional RAG systems generally follow this flow:

```text
User Question
      ↓
Retrieve Documents
      ↓
Generate Answer
```

This project evolves that architecture into a more intelligent workflow:

```text
                    ┌─────────────────────┐
                    │    User Question    │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │ Decide Retrieval?   │
                    └───────┬───────┬─────┘
                            │       │
                       No   │       │ Yes
                            ↓       ↓
                     Direct Answer  Retrieve
                                    ↓
                           Relevance Filtering
                                    ↓
                              Build Context
                                    ↓
                             Generate Answer
                                    ↓
                         Verify Answer Support
                                    ↓
                           Check Usefulness
                                    ↓
                       ┌────────────┴────────────┐
                       │                         │
                    Useful                   Not Useful
                       │                         ↓
                       ↓                  Rewrite Query
                    Final Answer               ↓
                                           Retry Retrieval
```

The final workflow combines multiple evaluation and correction mechanisms to make the RAG pipeline more robust.

---

## ✨ Key Features

* 🔍 **Conditional Retrieval**

  * The LLM decides whether external documents are actually required.
  * General questions can be answered directly without retrieval.

* 📚 **Document Retrieval**

  * Loads PDF documents and splits them into smaller chunks.
  * Uses OpenAI embeddings with FAISS for semantic search.

* 🎯 **Document Relevance Filtering**

  * Retrieved documents are evaluated before being used as context.
  * Irrelevant documents are removed from the generation process.

* 🧩 **Context Construction**

  * Relevant documents are combined into a context for answer generation.

* ✅ **Answer Support Verification**

  * Generated answers are checked against the retrieved context.
  * Answers can be classified as:

    * `fully_supported`
    * `partially_supported`
    * `no_support`

* 🔄 **Self-Reflection & Retry**

  * Unsupported or weak answers can trigger another generation/retrieval cycle.

* 📊 **Answer Usefulness Evaluation**

  * The system evaluates whether the generated answer is actually useful for the original question.

* ✍️ **Query Rewriting**

  * Poor retrieval can trigger an LLM-based query rewrite.
  * The rewritten query is optimized for vector retrieval.

* 🌐 **Web Search Extension**

  * The project also includes a web-based version using Tavily for questions requiring external/current information.

* 🧠 **LangGraph Workflow**

  * The complete pipeline is represented as a stateful graph with conditional routing.

---

## 🛠️ Tech Stack

| Technology             | Purpose                             |
| ---------------------- | ----------------------------------- |
| **Python**             | Core programming language           |
| **LangChain**          | LLM and retrieval components        |
| **LangGraph**          | Stateful RAG workflow orchestration |
| **OpenAI GPT-4o-mini** | LLM for decisions and generation    |
| **OpenAI Embeddings**  | Document embeddings                 |
| **FAISS**              | Vector similarity search            |
| **Pydantic**           | Structured LLM outputs              |
| **PyPDFLoader**        | PDF document loading                |
| **Tavily**             | Web search                          |
| **Jupyter Notebook**   | Development and experimentation     |
| **python-dotenv**      | Environment variable management     |

The initial implementation uses `text-embedding-3-large` for embeddings and `gpt-4o-mini` for generation and structured decisions.

---

## 📁 Project Structure

```text
SELF-RAG/
│
├── documents/
│   ├── Company_Policies.pdf
│   ├── Company_Profile.pdf
│   └── Product_and_Pricing.pdf
│
├── self_rag_step1.ipynb
├── self_rag_step2.ipynb
├── self_rag_step3.ipynb
├── self_rag_step4.ipynb
├── self_rag_step5.ipynb
├── self_rag_step6.ipynb
├── self_rag_step7.ipynb
│
├── self_rag_web.ipynb
│
└── README.md
```

The repository currently contains the seven step notebooks, a `documents` directory, and a separate web-search notebook.

---

# 📈 Learning Progression

The notebooks are intentionally structured as incremental improvements.

### Step 1 — Conditional Retrieval

`self_rag_step1.ipynb`

Introduces the basic Self-RAG architecture.

The system first determines whether retrieval is necessary:

```text
Question
   ↓
Need Retrieval?
   ├── No → Direct Generation
   │
   └── Yes → Retrieve Documents
```

The notebook uses LangGraph state, Pydantic structured output, FAISS retrieval, and OpenAI models.

---

### Step 2 — Document Relevance

`self_rag_step2.ipynb`

Adds a **relevance evaluation step**.

Instead of blindly trusting every retrieved document, the system evaluates whether each document can actually help answer the question.

```text
Retrieve
   ↓
Evaluate Relevance
   ↓
Keep Relevant Documents
```

This reduces the amount of irrelevant information passed to the LLM.

---

### Step 3 — Context Construction

`self_rag_step3.ipynb`

Adds a dedicated context-building stage.

The pipeline becomes:

```text
Question
   ↓
Retrieval Decision
   ↓
Retrieve
   ↓
Relevance Filtering
   ↓
Build Context
   ↓
Generate Answer
```

This creates a cleaner separation between retrieval, filtering, context construction, and generation.

---

### Step 4 — Answer Support Verification

`self_rag_step4.ipynb`

Introduces **IsSUP (Is Supported)** verification.

The generated answer is compared against the retrieved context.

Possible results:

```text
fully_supported
partially_supported
no_support
```

The system can use this evaluation to revise an answer that contains unsupported claims.

---

### Step 5 — Retry Mechanism

`self_rag_step5.ipynb`

Introduces a retry counter and a more controlled revision loop.

If the answer fails the support check, the system can attempt another generation/revision rather than immediately returning a potentially unreliable answer.

---

### Step 6 — Answer Usefulness

`self_rag_step6.ipynb`

Adds an **IsUSE** evaluation.

The system now evaluates not only:

> "Is the answer supported?"

but also:

> "Is the answer useful for the user's question?"

The answer can be classified as:

```text
useful
not_useful
```

A reason is also generated for the usefulness decision.

---

### Step 7 — Query Rewriting

`self_rag_step7.ipynb`

The final step introduces **retrieval query rewriting**.

If the existing retrieval process does not produce a useful answer, the LLM can transform the original question into a better retrieval query.

```text
User Question
      ↓
Retrieve
      ↓
Generate
      ↓
Support Check
      ↓
Usefulness Check
      ↓
Not Useful
      ↓
Rewrite Retrieval Query
      ↓
Retrieve Again
      ↓
Generate Better Answer
```

The notebook maintains state for the rewritten retrieval query and rewrite attempts.

---

# 🌐 Web Search Extension

`self_rag_web.ipynb`

This notebook extends the architecture beyond the internal PDF knowledge base.

When external information is required, the system can generate a web-search query and use **Tavily** to retrieve web results.

The web-search query is generated from the user's question and can account for recency when appropriate.

The extended architecture is:

```text
                  User Question
                       ↓
              Retrieval Decision
                 ↙         ↘
          Internal Docs     Web Search
                ↓               ↓
          Relevance Check   Web Results
                 ↘             ↙
                    Context
                       ↓
                  Generation
                       ↓
                Support Check
                       ↓
               Usefulness Check
                       ↓
                 Final Answer
```

---

# 📄 Knowledge Base

The initial RAG pipeline works with three PDF documents:

```text
documents/
├── Company_Policies.pdf
├── Company_Profile.pdf
└── Product_and_Pricing.pdf
```

Documents are loaded using `PyPDFLoader`, split using `RecursiveCharacterTextSplitter`, embedded using OpenAI embeddings, and indexed using FAISS. The initial configuration uses a chunk size of `600`, an overlap of `150`, and retrieves the top `4` documents.

---

# 🔑 Environment Variables

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
```

`TAVILY_API_KEY` is only required when using the web-search notebook.

> ⚠️ Never commit your `.env` file or API keys to GitHub.

---

# ⚙️ Installation

### 1. Clone the repository

```bash
git clone https://github.com/rameezuetian/SELF-RAG.git
cd SELF-RAG
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

### 3. Activate the environment

#### Windows

```bash
venv\Scripts\activate
```

#### Linux / macOS

```bash
source venv/bin/activate
```

### 4. Install dependencies

```bash
pip install langchain langchain-openai langchain-community langchain-text-splitters langgraph faiss-cpu pydantic python-dotenv pypdf
```

For web search:

```bash
pip install langchain-tavily
```

### 5. Configure API keys

Create `.env`:

```env
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
```

---

# ▶️ Running the Project

Open the project in Jupyter Notebook:

```bash
jupyter notebook
```

Then run the notebooks sequentially:

```text
self_rag_step1.ipynb
        ↓
self_rag_step2.ipynb
        ↓
self_rag_step3.ipynb
        ↓
self_rag_step4.ipynb
        ↓
self_rag_step5.ipynb
        ↓
self_rag_step6.ipynb
        ↓
self_rag_step7.ipynb
```

Finally, explore:

```text
self_rag_web.ipynb
```

to see the web-search extension.

---

# 🧠 Core Concepts Learned

This project demonstrates several important concepts in modern Generative AI:

* Retrieval-Augmented Generation (RAG)
* Self-RAG
* Agentic RAG
* Conditional routing
* LangGraph state management
* Structured LLM outputs
* Pydantic schemas
* Vector databases
* Semantic search
* Document relevance evaluation
* Answer faithfulness
* Hallucination reduction
* Self-reflection
* Query rewriting
* Retry loops
* Web search augmentation
* Context grounding
* LLM-based evaluation

---

# 🔄 RAG vs Self-RAG

### Traditional RAG

```text
Question
   ↓
Retrieve
   ↓
Generate
```

### Self-RAG

```text
Question
   ↓
Should I retrieve?
   ↓
Retrieve
   ↓
Are documents relevant?
   ↓
Generate
   ↓
Is answer supported?
   ↓
Is answer useful?
   ↓
Rewrite / Retry if necessary
   ↓
Final Answer
```

The main advantage is that the model is given mechanisms to **evaluate and improve its own retrieval and generation process**, rather than blindly following a fixed retrieve-then-generate pipeline.

---

# 🎯 Project Goals

The main goals of this project are:

1. Understand the architecture of Self-RAG.
2. Learn how to build stateful RAG workflows with LangGraph.
3. Implement conditional retrieval.
4. Filter irrelevant retrieved documents.
5. Verify generated answers against retrieved evidence.
6. Detect unsupported answers.
7. Evaluate answer usefulness.
8. Implement retry and revision mechanisms.
9. Rewrite poor retrieval queries.
10. Extend RAG with web search.

---

# 🚧 Future Improvements

Potential improvements include:

* Add streaming responses.
* Add conversation memory.
* Replace FAISS with a production vector database such as Pinecone or Chroma.
* Add source citations to final responses.
* Add evaluation metrics such as faithfulness, context relevance, and answer relevance.
* Add LangSmith tracing and evaluation.
* Build a Streamlit interface.
* Add hybrid search using keyword + semantic retrieval.
* Add reranking using a cross-encoder.
* Add configurable retry limits.
* Add automated test cases for each decision node.
* Deploy the application using Docker and a cloud platform.

---

# 📚 References

This implementation is inspired by the Self-RAG approach:

**Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection**

The project focuses on implementing the core ideas practically using modern LangChain and LangGraph components.

---

# 👨‍💻 Author

**Muhammad Rameez**

BS Computer Science
UET Narowal Campus

GitHub: [@rameezuetian](https://github.com/rameezuetian)

---

## ⭐ If you find this project useful

Give the repository a ⭐ and feel free to explore, modify, and extend the Self-RAG workflow.

```text
RAG → Self-RAG → Agentic RAG
```

This project is a practical exploration of that progression.
