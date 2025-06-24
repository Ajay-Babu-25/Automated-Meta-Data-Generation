#  AutoMeta: AI-Powered Smart Metadata Generator

AutoMeta is a Streamlit application that takes a document (PDF, DOCX, or TXT) and automatically extracts and generates metadata like a **smart title**, **summary**, **keywords**, **word count**, and **document type** using state-of-the-art Natural Language Processing (NLP) techniques.

---

##  Step-by-Step Breakdown (Code-Correlated)

### 1️) Environment Setup

Install all required libraries:

```bash
pip install streamlit transformers keybert sentence-transformers PyMuPDF python-docx pytesseract pdf2image pyyaml
```

 `requirements.txt` contains all these dependencies for Streamlit Cloud or local setup.

---

### 2️) Upload and Identify the Document

```python
uploaded = st.file_uploader(" Upload Document", type=["pdf", "docx", "txt"])
```

- Users upload `.pdf`, `.docx`, or `.txt` files.
- Stored temporarily using `NamedTemporaryFile`.

---

### 3️) Document Text Extraction

```python
def extract_text_from_pdf(path): ...
def extract_text_from_docx(path): ...
def extract_text_via_ocr(path): ...
```

- PDF: Extract with **PyMuPDF**
- DOCX: Extract with `python-docx`
- TXT: Directly read
- Scanned PDF: Use OCR (`pdf2image` + `pytesseract`)

---

### 4️) Summarization

```python
summarizer = pipeline("summarization", model="facebook/bart-large-cnn")
```

- Long documents are split into 1000-token chunks.
- BART summarizes each chunk.
- All summaries are concatenated into one final summary.

---

### 5️) Keyword Extraction

```python
kw_model = KeyBERT("sentence-transformers/all-MiniLM-L6-v2")
```

- Extracts top 8 key phrases from text.
- Uses sentence embeddings for semantic matching.

---

### 6️) Title Generation

```python
title_model = T5ForConditionalGeneration.from_pretrained("MBZUAI/LaMini-Flan-T5-248M")
```

- Prompt: `"Generate an appropriate title for this document: <text>"`
- Output is concise and meaningful.
- Better than using the first sentence of summary.

---

### 7️) Metadata Construction

```python
metadata = {
    "title": title,
    "summary": summary,
    "keywords": [...],
    "document_type": ...,
    "word_count": ...
}
```

Combines all outputs into a structured metadata dictionary.

---

### 8️) Display and Download Metadata

```python
st.json(metadata)
st.download_button(...)
st.code(yaml.dump(...))
```

- JSON is displayed and downloadable.
- YAML shown in code block.
- Full text shown in expandable textarea.

---

### 9️) Show Extracted Text

```python
with st.expander("📖 FULL TEXT EXTRACTED"):
    st.text_area("Extracted Text", text, height=200)
```

Provides visibility into raw text extracted.

---

##  Full Processing Pipeline

```
 Upload File
    ⬇
 Identify Format (PDF/DOCX/TXT)
    ⬇
 Extract Text (PyMuPDF / docx / OCR)
    ⬇
 Summarize Text (chunked → BART)
    ⬇
 Extract Keywords (KeyBERT)
    ⬇
 Generate Title (LaMini-Flan-T5)
    ⬇
 Build Metadata Object
    ⬇
 Display: JSON, YAML, Full Text
    ⬇
 Download JSON Metadata
```

---

##  How to Run Locally

```bash
streamlit run streamlit_app.py
```

---

##  How to Deploy on Streamlit Cloud

1. Push the repo to GitHub.
2. Go to [streamlit.io/cloud](https://streamlit.io/cloud).
3. Create a new app linked to your GitHub repo.
4. Ensure:
   - `streamlit_app.py` is the main file
   - `requirements.txt` is present
5. Deploy and enjoy!

---

##  Future Enhancements

- Add download button for YAML
- Add EPUB/HTML support
- Multiple file support
- Language detection or translation

---

##  Credits

- Summarization: [`facebook/bart-large-cnn`](https://huggingface.co/facebook/bart-large-cnn)
- Title generation: [`MBZUAI/LaMini-Flan-T5-248M`](https://huggingface.co/MBZUAI/LaMini-Flan-T5-248M)
- Keywords: [`KeyBERT`](https://github.com/MaartenGr/KeyBERT)

---

##  License

MIT License.
