# Nectar NL2SQL  

A personal project exploring Natural Language → SQL translation, semantic retrieval, and automated database syncing.

This project demonstrates a complete pipeline that converts natural language queries into SQL, validates and executes them, and falls back to semantic search if SQL generation fails. It also includes a background sync service that keeps a Chroma vector database updated with data from PostgreSQL.

---

## **Architecture diagram**:

<img width="1277" height="737" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/c50b953a-c38f-4636-a815-c020ccb8448e" />

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

## 🧠 How the Main Application Works

### **1. User enters natural language query**
Example:  
> "List employees working in the marketing department"

### **2. SQL Generation**
Uses Groq LLM (via the Inference API) to create a SQL query based on the NL input.  
LLM logic lives inside the `llm/` folder.

### **3. SQL Validation**
A custom validator checks:
- Syntax correctness  
- Safety (blocks dangerous keywords)  
- Table/column validity  

### **4. If valid → Execute on PostgreSQL**
The database helper modules inside `database/` handle the connection and query execution.

### **5. Summarization**
The summarizer converts raw DB results into a clean natural-language response.

### **6. If SQL is invalid → Semantic Fallback**
- The NL query is embedded  
- ChromaDB performs vector similarity search  
- Relevant content is retrieved  
- The summarizer produces the final response  

This ensures meaningful output even when SQL generation fails.

---

## 🔄 Sync Service (Background Embedding Sync)

The sync application (`sync_server.py`) keeps PostgreSQL and ChromaDB aligned.

### **Initial Run**
- If no `vector_db/` folder exists:  
  → A new Chroma vector database is created  
- A `sync_state.json` file is generated  
  → Stores the timestamp of last sync  

### **Continuous Sync (Every 1 Minute)**
- Runs asynchronously  
- Checks for updated rows in PostgreSQL  
- Re-generates embeddings only for changed data  
- Updates vectors in ChromaDB  

This makes the fallback search accurate and up to date.

---

## 🗂️ Project Overview

| File/Directory | Description |
| :--- | :--- |
| `main_server.py` | **Main NL2SQL Application:** Contains the primary server logic for processing natural language queries and generating SQL. |
| `sync_server.py` | **Background Sync Service:** Handles asynchronous tasks like data synchronization, update detection, and database embedding. |
| `requirements.txt` | Lists all necessary Python dependencies for the project. |
| `.env` | Environment configuration file for storing secrets, database credentials, and other settings. |

---

## 📂 Core Modules

| Directory | Purpose | Key Components |
| :--- | :--- | :--- |
| `llm/` | **Language Model Logic** | SQL generator, validator, result summarizer, and complex fallback logic implementations. |
| `database/` | **Data Access Layer** | Modules for connecting and interacting with **PostgreSQL** (relational data) and **Chroma** (vector store/embeddings). |
| `sync/` | **Synchronization** | Modules responsible for detecting database updates, preparing data, and generating/refreshing vector embeddings. |
| `frontend/` | **User Interface (UI)** | Stores HTML templates and static files (CSS, JavaScript, images) for the application's interface. |

---

## 🗄️ PostgreSQL Tables Used

- departments
- employee_projects
- employee_skills
- employees
- projects
- roles
- skills

For detailed schema, refer to the schema file inside the `database/` folder.

---

## ⚙️ Environment Setup

Create a `.env` file in the project root:

```
DB_HOST="localhost"
DB_PORT="5432"
DB_NAME="your_db_name"
DB_USER="postgres"
DB_PASSWORD="your_db_pswd"
GROQ_API_KEY=your_api_key_here
```
Make sure PostgreSQL is running locally with the above credentials.

---

## 📦 Installation

```bash
pip install -r requirements.txt
```

## ▶️ Running the Applications

### 1️⃣ Start the Main NL2SQL Application

The main application server is the primary interface for users.

```bash
python main_server.py
```
This launches the main system that:

- Accepts natural-language queries
- Generates SQL via Groq LLM
- Validates the SQL
- Executes on PostgreSQL
- Falls back to semantic search if SQL fails
- Summarizes results for the UI

### 2️⃣ Start the Sync Service

The sync system must be run in a **separate terminal window** to handle background tasks.

```bash
python sync_server.py
```
This background service performs crucial maintenance:

- Creates the vector database (e.g., Chroma) on its very first run.
- Maintains a sync_state.json file to track the latest sync timestamp.
- Performs asynchronous syncing checks at a regular interval (e.g., every 1 minute).
- Optimizes updates by only generating/updating embeddings for modified PostgreSQL rows.

---

## 🧩 Folder Breakdown

### `llm/`
Contains all LLM-related logic:
* **SQL generator** (Groq API integration)
* **SQL validator**
* **Summarizer** for query results
* **Semantic fallback** mechanism (utilizing Chroma vector search)

### `database/`
Responsible for data storage and retrieval:
* **PostgreSQL connection & operations**
* **Chroma vector DB operations**
* **Schema-related utilities**

### `sync/`
Handles the background syncing pipeline:
* Detect updated rows in PostgreSQL
* Regenerate embeddings for modified data
* Update the **ChromaDB** with new embeddings
* Manage `sync_state.json` file

### `frontend/`
Contains the application's user interface components:
* **HTML templates**
* **CSS / JS static assets**
* **UI for entering natural-language queries**

---

## 🎯 Project Goal

This project was built for **self-learning and exploration**, focusing on several key areas of modern application development and Natural Language Processing (NLP):

* **Transforming NL → SQL using LLMs:** Mastering the process of converting a user's **natural language (NL)** query into executable **SQL** code using a Language Model.
* **Validating SQL safely:** Implementing logic to ensure the generated SQL is both syntactically correct and safe to **execute** against the database.
* **Executing queries against PostgreSQL:** Gaining practical experience in connecting to and running queries against a **PostgreSQL** relational database.
* **Using ChromaDB for semantic retrieval fallback:** Utilizing a vector database like **ChromaDB** to provide **semantic search** or intelligent context when the direct SQL generation fails.
* **Building an automated embedding sync service:** Developing a reliable, background service to automatically detect database changes and update the required vector **embeddings**.
* **Structuring multi-component Python applications:** Learning to organize a complex application into logical, interconnected modules (main server, sync service, `llm/`, `database/`, etc.).

  


















