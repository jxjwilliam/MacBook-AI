## 📖 Popular AI UI Tools

| AI UI Tool                 | Deployment Type | Purpose/Description                                        |
|----------------------------|-----------------|------------------------------------------------------------|
| **Open-WebUI**             | Local           | A self-hosted web interface for running AI models locally. 用chrome插件替换之 |
| **chat.huggingface.co**    | Cloud           | Hugging Face's conversational AI tool for various models.  |
| **Google Colab**           | Cloud           | An online Jupyter notebook service for running AI models.  |
| **ChatGPT (OpenAI)**       | Cloud           | Conversational AI, accessible via web interface.           |
| **Ollama**                 | Local           | A local UI for running and interacting with AI models.     |
| **Stable Diffusion WebUI** | Local           | A web UI for generating images using Stable Diffusion models.|
| **KoboldAI**               | Local           | A local AI assistant for story writing and text generation.|
| **AutoGPT WebUI**          | Cloud/Local     | Interface for running autonomous GPT agents.               |
| **Gradio**                 | Cloud/Local     | A UI for creating and sharing machine learning demos.      |
| **RunPod**                 | Cloud           | Cloud-based UI for running various AI models and applications.|
| **DeepAI Text Generator**  | Cloud           | A cloud service for text generation using AI models.       |
| **MidJourney**             | Cloud           | AI-powered tool for creating images from text prompts.     |
| **Weights & Biases** | Cloud | Tracking and visualizing the performance of AI models during training<br>- Collaborating on AI/ML projects |
| **Streamlit** | Local/Cloud | Building interactive web interfaces for AI/ML applications<br>- Deploying and sharing AI demos and prototypes |
| **TensorFlow.js Playground** | Cloud | Experimenting with and deploying browser-based machine learning models<br>- Exploring the capabilities of client-side AI |
| | | |

## 📖 Comparison of AI Frameworks

| Feature/Framework         | LlamaIndex (formerly GPT Index)                                | LangChain                                        | Other Similar Frameworks                        |
|---------------------------|----------------------------------------------------------------|--------------------------------------------------|------------------------------------------------|
| **Primary Purpose**       | Connects LLMs to various data sources for retrieval and integration | Building chains of LLMs with tools for complex applications | Varies (e.g., Pinecone for vector storage, Haystack for NLP pipelines) |
| **Core Functionality**    | Data indexing, retrieval-augmented generation (RAG)             | Chaining LLMs with tools like memory, APIs, agents | Depends on the framework; could include NLP, vector search, etc. |
| **Customizability**       | High; allows for custom data retrieval pipelines                | High; extensive support for custom LLM workflows  | Varies by framework, generally high in specialized areas |
| **Ease of Use**           | Moderate; requires understanding of data integration            | Moderate; requires familiarity with chaining concepts | Varies; often requires domain-specific knowledge |
| **Integration**           | Supports multiple data sources, databases, and APIs            | Integrates with many tools, including LLMs and other AI services | Depends on the framework; usually supports integration with LLMs or data stores |
| **Community & Support**   | Growing community, active development                          | Large and active community, with extensive documentation | Varies; some have strong community support, others are more niche |
| **Use Cases**             | Data-driven applications, retrieval-enhanced generation         | Complex LLM-based applications, multi-step workflows | Varies; specific to the framework's domain (e.g., NLP, search, etc.) |
| **Examples**              | Building chatbots with specific document retrieval, custom search engines | Conversational agents, LLM-powered apps with memory | Vector search engines, NLP processing pipelines |

## 📖 LlamaIndex (formerly GPT Index)

- **Purpose**: LlamaIndex is a data framework that allows you to connect LLaMA (or other large language models) to various data sources, making it easier to perform tasks like `information retrieval`, `data querying`, and `document search`. LlamaIndex is particularly focused on helping users build AI applications that require the integration of LLMs with structured or unstructured data.
- **Features**:
  * `Data Integration`: LlamaIndex facilitates the connection of LLaMA models to external data sources like databases, document stores, or APIs, enabling the LLMs to perform specific tasks based on the data.
  * `Retrieval-Augmented Generation (RAG)`: LlamaIndex can enhance the capabilities of LLMs by incorporating external knowledge during generation, allowing for more accurate and contextually relevant outputs.
  * `Customizable Pipelines`: Users can customize the way data is retrieved, processed, and presented by the LLaMA model, tailoring the output to specific use cases.

### 🍓 LlamaHub

### 🍓 LlamaLab

- A repo dedicated to building cutting-edge projects using LlamaIndex.

### 🍓 [LlamaCloud](https://github.com/llamacloud/llamacloud)

- LlamaCloud is a cloud-based platform for building AI applications.
- It provides a managed service for running LLaMA models, as well as a set of tools for integrating these models with external data sources.
- LlamaCloud also offers automated model deployment, scaling, and monitoring.

### 🍓 [LlamaParse](https://github.com/connor-jennings/llamaparse)

- A framework for parsing and generating code using LLMs.
- Supports parsing of many programming languages.

### 🍓 [A real world full-stack application using LlamaIndex](https://github.com/run-llama/sec-insights)

- LlamaIndex RAG, secinsights.ai, polygon.io
- .devcontainer
- backend: FastAPI, Docker, SQLAlchemy, PGVector, LLamaIndex, OpenAI
- frontend: React/Next.js, Tailwind CSS
- Server-Sent Events

### 🍓 Your choice of 3 back-ends

- `Next.js`: if you select this option, you’ll have a full-stack Next.js application that you can deploy to a host like `Vercel` in just a few clicks. This uses `LlamaIndex.TS`, our TypeScript library.
- `Express`: if you want a more traditional `Node.js` application you can generate an Express backend. This also uses `LlamaIndex.TS`.
- `Python FastAPI`: if you select this option, you’ll get a backend powered by the llama-index Python package, which you can deploy to a service like `Render` or `fly.io`.

### 🍓 Flask vs FastAPI

- Use `Flask` if you need a simple, flexible framework for a small to medium-sized application or if you’re building a traditional web application.
- Use `FastAPI` if you need to build a high-performance API, especially if you require asynchronous capabilities and automatic documentation.

## 🌐 RAG Stack

Doc -> Chunk -> Embedding -> Vector Store -> Retriever -> LLM -> Response

### 🟢 Adding Agentic Layers to RAG

- `Query -> Agents -> RAG -> Agents -> Response`
- Routing
- Query Planning
- Tool Use
- ReAct: Reasoning + Acting with LLMs
- Tavily: Synergizing (协同效应) Reasoning and Acting in Language Models
- Memory
- Chain-of-Thought

### 🍓 Llama-Index Multi-Document Agent

- VectorStoreIndex
- SummaryIndex
- ObjectIndex
- QueryEngineTool
- OpenAIAgent
- FnRetrieverOpenAIAgent
- 
