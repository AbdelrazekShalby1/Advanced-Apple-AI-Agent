# Advanced Apple AI Agent

An n8n retrieval augmented generation workflow for exploring Apple earnings
documents. It ingests documents from Google Drive into Qdrant and exposes a chat
agent that searches the indexed reports before answering.

## What is included

- `workflow.json`: an importable n8n workflow with 18 nodes.
- Google Drive polling for newly created files and manual folder indexing.
- Binary file downloading, document loading, and recursive text splitting.
- Gemini embeddings stored in the `advanced_apple_rag` Qdrant collection.
- A Groq chat model connected to an AI agent and a Qdrant retrieval tool.

```text
Google Drive -> Download -> Document loader / splitter -> Gemini embeddings -> Qdrant
                                                                                 |
Chat trigger -> AI agent -> Groq model --------------------------------> Retrieval tool
```

## Requirements

- An n8n installation supporting the node types and versions in `workflow.json`.
- Google Drive OAuth credentials with access to your source folder.
- Google Gemini embeddings credentials, Groq credentials, and Qdrant credentials.
- Your own Apple reports, with permission to process them through those services.
- A Qdrant collection compatible with the selected embedding model and output size.

## Import and configure

1. Create an n8n workflow and import `workflow.json` from a file.
2. Assign credentials to both Drive download nodes, the Drive trigger and search
   nodes, both Qdrant nodes, both Gemini embedding nodes, and the Groq model node.
3. Replace `YOUR_GOOGLE_DRIVE_FOLDER_ID` in the Drive trigger and manual search
   nodes with your source folder ID.
4. Verify that both Qdrant nodes target the same collection and both embedding
   nodes use the same model. The exported model is `models/gemini-embedding-001`.
5. In `Qdrant Vector Store1`, replace `YOUR_GOOGLE_DRIVE_FILE_ID` in the metadata
   filter with an indexed file ID. Remove that optional filter if you want to
   search all indexed reports. Check the stored `metadata.File_ID` field first.
6. Inspect the Groq model choice. The original export selects
   `openai/gpt-oss-safeguard-20b`; choose an available chat model supporting the
   agent's tool calls if that model does not fit your account or task.
7. Run the manual trigger to index existing files, inspect each node's output,
   then test the chat with a question grounded in an indexed report.
8. Enable the Drive trigger only when configuration and tests are complete.

The import is inactive and its chat trigger is private by default. n8n credential
references, pinned data, instance metadata, workflow IDs, webhook IDs, and original
Drive identifiers have been removed from the publishing copy. No credentials or
financial reports are bundled.

## Example questions

- What does the indexed report say about quarterly revenue?
- Summarize the outlook discussed in the uploaded earnings document.
- Compare the margins across the reports currently indexed in the knowledge base.

Answers depend on the supplied reports and retrieval settings. This repository
contains the workflow definition, not a hosted service or a prepopulated vector
database. Import structure was checked locally; live n8n execution and provider
calls require your credentials and have not been reproduced here.
