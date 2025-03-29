
## 📖 LangChain

The Python-specific portion of `LangChain`'s documentation covers several main modules, each providing examples, how-to guides, reference docs, and conceptual guides. These modules include:

1. **Models**: Various model types and model integrations supported by `LangChain`.
1. **Prompts**: Prompt management, optimization, and serialization.
1. **Memory**: State persistence between chain or agent calls, including a standard memory interface, memory implementations, and examples of chains and agents utilizing memory.
1. **Indexes**: Combining `LLMs` with custom text data to enhance their capabilities.
1. **Chains**: Sequences of calls, either to an LLM or a different utility, with a standard interface, integrations, and end-to-end chain examples.
1. **Agents**: `LLMs` that make decisions about actions, observe the results, and repeat the process until completion, with a standard interface, agent selection, and end-to-end agent examples. 
   
## 📖  LangChain Integrations

LangChain 是一个强大的框架，旨在帮助开发人员使用语言模型构建端到端的应用程序1。它提供了一套工具、组件和接口，可以简化由大型语言模型 (LLM) 和聊天模型提供支持的应用程序的创建过程1。LangChain 可以轻松管理与语言模型的交互，将多个组件链接在一起，并集成额外的资源，例如 API 和数据库。

- Components and Chains
- Prompt Templates and Values
- Example Selectors
- Output Parsers
- Indexes and Retrievers
- Chat Message History
- Agents and Toolkits

### 🥃 Components and Chains

- docs -> split, vectorstores, indexing, embedding, victorstores
- Indexing: split, chunk, embedding,
- Retrieval and Generation: Prompt, LLM, Chain, dict, template, Run, Post-processing, Chain, Question

### 🥃 Vector Persistant Storage

- ElasticVectorSearch (Document Loaders) -> Documnet Embeddings | Document Transformers -> ElasticSearch
    * Unstructured Public: youtube, wikipedia, news, books, articles, papers, etc.
    * Structured Public: Datasets, OpenWeather
    * Unstractured Properlatary: Company docs, emails, etc.
    * Structured Properlatary: Stripe, xls
- Pinecone
- LanceDB
- Redis
- Chroma
- MongoDBAtlasVectorSearch
- FAISS

### 🥃 LangChain Components

| Concept   | Description                                                                 | Tip/Note                                                       |
|-----------|-----------------------------------------------------------------------------|----------------------------------------------------------------|
| LangChain | A framework for building applications with LLMs, supporting retrieval-augmented generation (RAG), conversational agents, and more. | Focus on chaining together various language model tasks for complex workflows. |
| LangGraph | A framework for defining and executing language-based workflows, using graph-based structures for more flexible, non-linear workflows. | Ideal for scenarios requiring dependency tracking and branching in task execution. |
| LangSmith | A debugging and evaluation toolset for applications using LLMs, focusing on testing and monitoring LLM performance. | Use to optimize performance and debug workflows in LangChain-based applications. |
| LangHub   | A repository or platform for sharing and discovering pre-built LangChain modules, agents, and tools. | Explore to find reusable components for faster development and prototyping. |

### 🥃 langchain.schema

AIMessage, HumanMessage, SystemMessage

```python
messages = [
  SystemMessage(content="You are a helpful assistant."),
  HumanMessage(content="I am a bot, what are you?")
]
```

### 🥃 PromptyTemplate

```python
PromptTemplate(
  template="Tell me a joke about {topic}.",
  input_variables=["topic"],
)
```

### 🥃 invoke

### 🥃 from langchain.chains import LLMChain

`chains = LLMChain(llm, prompt)`

### 🥃 splitter

- RecursiveCharacterTextSplitter
- CharacterTextSplitter
- SentenceTextSplitter
- TokenTextSplitter
- CustomTextSplitter

### 🥃 embedding

- OpenAIEmbeddings(model="text-embedding-ada-002") # charge
- OpenAIEmbeddings(model="text-embedding-3-small") # cheaper
- HuggingFaceEmbeddings(model_name="sentence-transformers/all-mpnet-base-v2") # free, locally,

### 🥃 search type

- similarity: `{"k": 3, "method": "similarity"}`
- Maximum Marginal Relevance (MMR): `{"k": 3, "method": "mmr", "fetch_k": 20, "lambda_mult": 0.5}`
- Similarity Score Threshold (临界点): `{ "k": 3, "method": "similarity_score_threshold", "score_threshold": 0.1 }`

### 🥃 retriever

`combined_input = (... + "\n\n".join([doc.page_content for doc in relevant_docs]) + "\n")`

### 🥃 

### 🥃 

### 🥃 

### 🥃 

### 🥃 

### 🥃 
