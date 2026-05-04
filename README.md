RAG FAQs and Implementation Concepts
-------------------------------------------
Q1: What is Chunking and why is it necessary for RAG?
Answer: Chunking is the process of breaking down large documents into smaller, manageable pieces of text called "chunks".
It is necessary for RAG (Retrieval-Augmented Generation) because:

Context Window Limits: LLMs have a maximum number of tokens they can process at once. Chunks ensure the model isn't overwhelmed by massive documents.

Precision: Smaller chunks allow the system to find the exact paragraph that answers a user's question, rather than providing an entire 50-page PDF.

Relevance: It helps in filtering out noise, ensuring only relevant information is "fed" to the LLM during the generation phase.

Q2: What is the difference between chunk_size and chunk_overlap?
Answer:

chunk_size: The maximum number of characters (or tokens) that a single chunk can contain.

chunk_overlap: The amount of text that is shared between two consecutive chunks.

Q3: Why is Chunk Overlap a critical concept?
Answer: Chunk overlap is essential to preserve context. If you cut a document strictly every 1,000 characters, a sentence might get split in half, causing the meaning to be lost in both chunks. By overlapping (usually 10-20%), the end of Chunk A is repeated at the start of Chunk B, ensuring that semantic information "flows" across the boundaries and stays intact for the embedding model.

Q4: What are the different types of chunking available in LangChain?
Answer:

Length-based (RecursiveCharacterTextSplitter): The most common method. It splits text based on a list of characters (like \n\n, \n, .,  ) until chunks are small enough.

Text Structure-based: Splits text based on specific formats like Markdown, HTML, or code (Python/JS) to keep sections or headers together.

Document Structure-based: Uses the inherent structure of files (like PDF headers or LaTeX) to determine where a break should occur.

🔢 The Math: Embeddings
Q5: What is an Embedding and why is it required?
Answer: An embedding is a mathematical representation of text in a high-dimensional vector space. Machines don't understand language; they understand numbers.

Semantic Meaning: It captures the relationship between words. "Bruce Wayne" and "Batman" will be mathematically closer than "Batman" and "Cooking".

Searchability: It allows the system to perform a "Similarity Search" by comparing the vector of a user's question to the vectors of your stored chunks to find the closest match.

Q6: Why did we use asia-south1 as the location for Vertex AI?
Answer: Choosing the correct region (like Mumbai for users in India) reduces latency—the time it takes for data to travel from your machine to the Google servers. It also ensures you are using a region where your chosen model (text-embedding-005) is available and active within your GCP project.

🗄️ The Storage: Vector Databases (ChromaDB)
Q7: What is ChromaDB and how is it used?
Answer: ChromaDB is an open-source Vector Database designed for AI applications. It stores your text chunks along with their corresponding embedding vectors. It allows for extremely fast "nearest neighbor" searches, enabling the "Retrieval" part of RAG.

Q8: Why do we insert Document chunks into the DB instead of raw embeddings?
Answer:

Pros of Chunks: When you pass the Document object, ChromaDB stores the text, the math (vector), and the metadata (like URLs or titles) together. This allows the system to return readable text and sources to the user.

Cons of raw Embeddings: If you only store the numbers, you lose the original text. You would find a match, but you wouldn't know what the actual words were to show the user.

Q9: Why did we implement a "Batching Loop" for our embeddings?
Answer: Cloud APIs like Google Vertex AI have Payload Limits. For example, the text-embedding-005 model has a limit of 20,000 tokens per request. If your Wikipedia page generates 34,000 tokens, the API will throw a 400 Invalid Argument error. We use a batching loop to send data in smaller groups (e.g., 50 chunks at a time) to stay under these limits while processing large datasets.
