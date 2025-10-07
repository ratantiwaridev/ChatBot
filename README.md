# 🤖 RAG-Powered AI Chatbot for Google Drive Documents
A custom, domain-specific chatbot built using n8n AI Agents to deliver accurate, context-aware answers by leveraging Retrieval-Augmented Generation (RAG) across documents stored in Google Drive and indexed in Pinecone.

## ✨ Features
Retrieval-Augmented Generation (RAG): Ensures the AI responses are grounded in your specific documents rather than general knowledge, minimizing hallucinations.

Domain-Specific Q&A: Knowledge is dynamically retrieved from a designated Google Drive folder, making the chatbot an expert on your unique data (policies, manuals, research, etc.).

Automated Ingestion Pipeline: n8n workflows manage the entire process, from monitoring Google Drive for new files to chunking, embedding, and indexing in Pinecone.

Scalable Vector Store: Uses Pinecone to efficiently store and search document embeddings for fast, semantic retrieval.

Tech Stack: Seamlessly integrates n8n's workflow automation with powerful cloud AI services.

## ⚙️ Tech Stack
Component	Role	Notes
n8n	Workflow Automation	Hosts the ingestion pipeline and the AI Agent logic.
Google Cloud APIs	AI/LLM & Embeddings	Used for the core AI models and generating vector embeddings.
Google Drive	Knowledge Source	The central repository for all domain-specific documents.
Pinecone	Vector Database (RAG)	Stores vector embeddings for semantic search and retrieval.

# Export to Sheets
## 🚀 How It Works
The project is typically structured into two primary n8n workflows:

## 1. Document Ingestion Workflow (Indexer)
Trigger: A Manual Trigger (or a Google Drive Trigger for full automation) initiates the indexing process.

- Google Drive Node: Downloads documents (e.g., PDFs, text, Docs) from a specified folder.

- Text Splitter: Breaks large documents into smaller, manageable chunks.

- Google Cloud/Gemini Embeddings Node: Generates a unique vector embedding (a numerical representation) for each chunk.

- Pinecone Vector Store Node: Stores the text chunks and their corresponding embeddings into a designated Pinecone index/namespace.

## 2. Chat & Retrieval Workflow (Chatbot)
- Chat Trigger Node: Listens for incoming user questions (e.g., via a public URL or custom front-end).

- AI Agent Node: This core node is configured with a Vector Store Tool.

- Vector Store Tool (Pinecone): Converts the user's question into an embedding and queries Pinecone to retrieve the Top K most relevant document chunks.

- Google Cloud Chat Model: Feeds the original question PLUS the retrieved context to the LLM.

- Response: The LLM generates a grounded answer based only on the provided context, which is then returned to the user via the Chat Trigger.

## 🛠️ Setup and Installation
### Prerequisites
**Before running the workflow, you must have the following accounts and credentials:**

- n8n Instance: Self-hosted or n8n Cloud instance.

- Google Cloud/AI API Key: For your chosen LLM and Embedding models (e.g., Gemini).

- Google Drive Account: With a folder containing your documents.

- Pinecone Account: An active API key and a created index (e.g., rag-knowledge-base).

## n8n Configuration Steps
- Step 1: Import Workflows
    1. Open your n8n instance.

    2. Import the provided n8n workflow JSON files (usually named ingestion-workflow.json and chatbot-workflow.json).

- Step 2: Configure Credentials
    **You must set up the following credentials in your n8n Settings -> Credentials section:**

### Credential	Type	Details to Provide
- Google Drive	OAuth2/API Key	Connect to allow n8n to read/download your documents.
- Google AI / Gemini	API Key	Your key for running the LLM and embedding generation.
- Pinecone	API Key	Your Pinecone API Key and Environment URL.

### Export to Sheets
- Step 3: Configure Workflows
A. Ingestion Workflow (Indexer)

- Open the Google Drive Node: Specify the Folder ID where your source documents are located.

- Open the Pinecone Vector Store Node: Set the correct Index Name and Namespace (must match the index you created in Pinecone).

- Run the Indexer: Execute this workflow manually (or set a trigger) to process and store your documents into Pinecone.

B. Chatbot Workflow

- Open the Pinecone Vector Store Node (Query Mode): Ensure the Index Name and Namespace match the settings from the ingestion workflow.

- Open the Google Cloud Chat Model Node: Select your configured Google AI credential.

- Activate the Chatbot: Set the main Chatbot workflow to Active.

- Get Public URL: Open the Chat Trigger node, copy its public URL, and use this URL to start chatting!

## ⚠️ Important Considerations
- Chunking Strategy: The Text Splitter node's settings (chunk size and overlap) are critical for RAG performance. Adjust these based on the average length and structure of your documents.

- Security: Ensure that the public URL for the Chat Trigger is only shared with intended users, or integrate it securely into a private application.

- Cost Management: Monitor your usage of Google Cloud and any other LLM provider APIs, as RAG can generate significant embedding and completion calls.
