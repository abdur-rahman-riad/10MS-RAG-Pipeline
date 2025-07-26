**Setup Guide: Use Google Colab or Jupyter Notebook.**<br>
**Used tools / Library /Package:** <br>
Install: 
```bash
!apt-get install -y poppler-utils tesseract-ocr tesseract-ocr-ben
!pip install gdown pytesseract pdf2image faiss-cpu sentence-transformers transformers gradio
```
Imports:
```bash
import gdown
from pdf2image import convert_from_path
import pytesseract
import unicodedata
import re
from sentence_transformers import SentenceTransformer
import numpy as np
import faiss
from transformers import pipeline
import gradio as gr
```

Then Load the dataset and run the code. for dataset load:
```bash
file_id = "1YVPzZgXp6nyklR6SfNmLtV8tZJXqJtMH"
file_url = f"https://drive.google.com/uc?id={file_id}"
output = "HSC26-Bangla1st-Paper.pdf"
gdown.download(file_url, output, quiet=False)
```

---

### 🔗 Live Demo App

[Click Here to try the Live App](https://dd53fcb18de85b71a3.gradio.live/)

---

1. **What method or library did you use to extract text from the PDF, and why? Did you face any formatting issues?**  
   For this task, I used OCR (Optical Character Recognition) with the pytesseract library to extract text. This library converts PDF pages into images and then pulls the text from those images. I chose it because it’s effective for handling scanned or complex PDFs where direct text extraction doesn’t work well.  
   **Challenges**: I ran into a few issues. Words and sentences sometimes got broken up, and the text wasn’t always detected correctly. Formatting was tricky, especially with multiple-choice questions (MCQs), as the structure got messy. I tried extracting text without OCR, but it didn’t work for this PDF.

2. **What chunking strategy did you use (e.g., paragraph-based, sentence-based, or character limit)? Why do you think it’s good for semantic retrieval?**  
   I went with paragraph-based chunking, combined with a character limit, because this is a book, and paragraphs naturally hold related ideas together. This approach helps the embedding model understand the context and relationships within the text, which is great for semantic retrieval. The overlap between chunks also ensures no important information gets missed, keeping the meaning intact.

3. **What embedding model did you use, and why? How does it capture the meaning of the text?**  
   I used the paraphrase-multilingual-MiniLM model for this task. It’s designed to handle multiple languages, including Bengali, which was perfect for this project. This model uses a transformer architecture with self-attention to understand the context and relationships between words in a chunk, turning text into meaningful embeddings that capture its essence.

4. **How do you compare the query with your stored chunks? Why did you pick this similarity method and storage setup?**  
   I used FAISS with an L2 (Euclidean) distance metric (IndexFlatL2) to compare the query embedding with the stored chunk embeddings. L2 measures how close the query and chunks are in the vector space. I chose it because it’s simple, accurate, and works well for small-to-medium datasets. Pairing it with SentenceTransformer embeddings made it a reliable and straightforward choice.

5. **How do you make sure the question and document chunks are compared meaningfully? What happens if the query is vague or lacks context?**  
   The paraphrase-multilingual-MiniLM-L12-v2 model creates rich embeddings that capture the meaning of both the query and chunks. By putting them in the same vector space, it ensures comparisons focus on meaning, not just exact words. Paragraph-based chunking with overlap helps keep enough context for accurate matches.  
   If a query is vague, the model might struggle to find relevant chunks because it lacks clear meaning. It’ll still return the closest matches based on L2 distance, but they might not be spot-on. For example, if a query misses key details (like an MCQ question number), the results could be related but not precise.

6. **Are the results relevant? If not, what could make them better (e.g., better chunking, a stronger embedding model, or a larger document)?**  
   The results are mostly relevant but sometimes off due to inaccuracies. To improve, we could enhance OCR to better handle Bengali characters and reduce errors. The model has some limitations, so switching to a more advanced one might help. Better chunking and preprocessing steps could also make the whole pipeline more accurate.
