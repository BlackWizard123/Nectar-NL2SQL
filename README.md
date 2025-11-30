# Nectar NL2SQL  

A personal project exploring Natural Language → SQL translation, semantic retrieval, and automated database syncing.

This project demonstrates a complete pipeline that converts natural language queries into SQL, validates and executes them, and falls back to semantic search if SQL generation fails. It also includes a background sync service that keeps a Chroma vector database updated with data from PostgreSQL.

---

## 🚀 Overview

The system is divided into **two independent applications**:

### **1. Main Application (`main_server.py`)**
Handles:
- Frontend interaction  
- NL → SQL generation using Groq LLM  
- SQL validation  
- PostgreSQL query execution  
- Semantic fallback using ChromaDB  
- Summarization of results  

### **2. Sync Application (`sync_server.py`)**
A background process responsible for:
- Initial vector DB creation  
- Maintaining sync between PostgreSQL and ChromaDB  
- Updating only modified rows  
- Running asynchronously every 1 minute  

This allows the main system to always rely on fresh embeddings for semantic retrieval.

---
