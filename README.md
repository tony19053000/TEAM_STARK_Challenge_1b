## 📝 Approach Explanation – TEAM_STARK_Challenge_1b

The goal of this project is to analyze a set of PDF documents and extract the most relevant content based on a given persona and their specific task (also called "job to be done"). This is achieved through a combination of PDF parsing, sentence embedding, similarity-based ranking, and structured output generation.

---

### 🔍 Problem Understanding

Organizations often deal with large volumes of documents, and identifying useful information manually is time-consuming. Our solution helps solve this by automatically extracting the top sections most relevant to a user persona and their intent — such as an HR manager looking for form-building features.

The input to our system includes:
1. A list of PDF documents
2. A JSON input file describing the persona and task

The output is a well-structured JSON summarizing the most important paragraphs with relevance scores and bullet-pointed insights.

---

### 🧱 Methodology

Our pipeline is built with the following stages:

#### 1. **PDF Parsing**
We use the PyMuPDF (`fitz`) library to extract clean, structured text from all pages of the PDFs. The parser loops through each file and each page, collecting non-empty text blocks along with filename and page number metadata.

#### 2. **Text Embedding**
To understand semantic meaning, we use the `bge-small-en-v1.5` model from `sentence-transformers`. For each paragraph, we create a query that combines the persona's task with the paragraph content, separated by `[SEP]`. This way, the embedding is contextualized specifically for the task.

#### 3. **Paragraph Ranking**
Each paragraph is scored based on its similarity to a task-relevant representation. We group paragraphs by document and calculate cosine similarity between each embedding and the mean embedding of the document. Only those with high scores (above a threshold) are retained. This ensures we extract only the most relevant sections.

#### 4. **Output Formatting**
The top-ranked paragraphs are passed into a formatter that generates:
- `extracted_sections`: Metadata about the paragraph (title, page number, rank)
- `subsection_analysis`: A bullet-point summary using a rule-based approach to enhance readability

We also assign meaningful section titles by heuristically analyzing the first few lines of each paragraph.

---

### ⚙️ Why This Works

This approach balances precision and efficiency:
- Using a sentence-transformer gives strong contextual understanding
- Paragraph-level analysis ensures coverage across multiple documents
- Rule-based summaries provide clarity without requiring an LLM

The system is optimized to run on CPU within tight constraints, making it suitable for deployment in resource-limited environments.

---

### 📦 Output & Usability

The final output is saved as `output/output.json`, ready to be consumed by downstream systems, UIs, or decision-makers. Our method ensures that all input documents are considered and only the most relevant information is returned.

This approach is robust, modular, and scalable — and can be adapted to various enterprise use cases such as contract review, policy summarization, or product documentation analysis.
