# RAG Maintenance Assistant

A retrieval-augmented generation (RAG) assistant for heating and heat-pump
maintenance questions. It answers technician questions from technical
handbooks and uses a student/teacher review workflow to improve the initial
answer.

The project was built as a practical demonstration of a RAG pipeline combined
with an RLAIF-inspired feedback workflow. It does not fine-tune a model; it
collects structured preference pairs that could be used for DPO-style tuning
in a later project.

## What it does

A technician asks a question in German. The system searches three technical
handbook PDFs, retrieves the most relevant passages, and generates an initial
answer with a smaller language model. A larger language model then reviews the
answer, corrects technical errors where necessary, and improves clarity.

If the student and teacher answers differ sufficiently, the system stores a
preference pair in JSONL format:

- `rejected`: the initial student answer
- `chosen`: the reviewed teacher answer
- `prompt`: the original technician question
- `context`: the retrieved handbook passages
- `similarity`: text similarity between both answers

These pairs could later be used for DPO-style preference tuning with tools
such as Hugging Face TRL or Unsloth.

## Example questions

<img width="1791" height="331" alt="rag-maintenance-assistant-demo png" src="https://github.com/user-attachments/assets/13813c95-0e16-443d-b454-dafd19b047d8" />

<img width="1785" height="215" alt="rag-maintenance-assistant-demo2 png" src="https://github.com/user-attachments/assets/c4d8ee7e-0582-4add-b8d0-739ca44a4e30" />


The questions are in German because the source handbooks are German. The
assistant is designed to answer in German as well.

## How it works

```text
Technician question
        ↓
FAISS semantic search
        ↓
Four relevant handbook passages
        ↓
Student model generates initial answer
        ↓
Teacher model reviews and improves the answer
        ↓
Preference pair saved when answers differ sufficiently
```

The assistant does not invent an answer when the handbooks do not contain the
required information. This behaviour is intentional: maintenance advice should
be traceable to the available documentation.

## Tech stack

| Component | Technology |
|---|---|
| Language | Python |
| RAG framework | LangChain |
| PDF parsing | PyMuPDF |
| Vector search | FAISS |
| Embeddings | `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` |
| LLM provider | Groq API |
| Student model | `openai/gpt-oss-20b` |
| Teacher model | `openai/gpt-oss-120b` |
| Feedback format | JSONL |
| Environment | Google Colab |

## Repository structure

```text
.
├── .gitignore
├── LICENSE
├── README.md
└── rag_maintenance_assistant.ipynb
```

## Running the notebook

The project was developed and tested in Google Colab.

1. Open the notebook in Colab.
2. Add a Groq API key under **Secrets** using the name `Groq_KEY`.
3. Enable notebook access for the secret.
4. Run the cells in order.
5. Use the demo questions in the final cell to test the full workflow.

The API key is loaded through Colab Secrets and is not stored in the notebook.

## Requirements

```text
langchain
langchain-groq
langchain-community
langchain-text-splitters
faiss-cpu
pymupdf
sentence-transformers
```

## Notes and limitations

- The project is a prototype, not a production-ready maintenance system.
- The handbooks are not included in this repository.
- The preference pairs are collected for potential future tuning; no model is
  trained in this repository.
- The student and teacher models can be changed in the notebook configuration.

## License

This project is released under the MIT License.
