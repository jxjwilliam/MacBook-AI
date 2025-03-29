
### 🥃 Transformers & Libs

- Transformers
- Datasets
- Evaluate
- PEFT model: Lora adapters
- Accelerate
- Optimum
- Gradio, Streamlit

### 🥃 Transformers Installation and Setup

- miniconda
- `conda create -n transformers python=3.9`
- pypi
- `pip install pytorch`, or `conda install`
- CUDA: TensorRT
- VS Code, Python, Jupyter, remote ssh
- `pip install transformers datasets evaluate peft accelerate gradio optimum sentencepiece`
- `pip install jupyterlab scikit-learn pandas matplotlib tensorboard nltk rouge`

### 🥃 Pipeline

这是一个非常方便的工具，它允许你快速使用预训练模型进行各种NLP任务，如情感分析、命名实体识别、问答等，而无需深入了解模型的内部工作原理。

### 🥃 transformers

### 🥃 Tokenizer

将文本转换为模型可以处理的数据。Tokenizer 通常包括两个步骤：标记化和转换为输入 ID。在 Python 中，可以使用 transformers 库中的 AutoTokenizer 类来加载和使用预训练模型的 Tokenizer。

```python
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
tokenizer.save_pretrained("path_to_save")
```

### 🥃 Adpapter

在预训练模型中插入一些少量的参数层，使得模型可以在特定任务上进行微调，而不需要改变原有的预训练参数。

### 🥃 Model

如 Transformer 或 BERT。

### 🥃 LLaVA (Large Language and Vision Assistant)

- 用于理解和处理视觉和语言数据
- 是一个端到端训练的大型多模态模型，旨在根据视觉输入（图像）和文本指令理解和生成内容。它结合了视觉编码器和语言模型的功能来处理和响应多模态输入。
- OCR
- CLIP: Encode, Decode


### 🥃 vLLM (virtual Large Language Model)

- `vLLM` is a fast and easy-to-use library for LLM **inference** and serving.
- PagedAttention
- Unsloth uses vLLM ?

### 🥃 Chinese Background

- `Llama-index`, `vLLM`, `LLaVa`, and `Unsloth` have connections to Chinese researchers or institutions
- Dify, Flowise

### 🥃 Anthropic BM25 (Best Match 25)

是一种排名函数，它使用词汇匹配来查找精确的单词或短语匹配。它对于包含唯一标识符或技术术语的查询特别有效。
BM25 的工作原理基于 TF-IDF（术语频率-逆文档频率）概念。 TF-IDF 衡量一个单词对于集合中的文档的重要性。 BM25 通过考虑文档长度并对术语频率应用饱和函数来对此进行细化，这有助于防止常见单词主导结果。

### 🥃 HuggingChat

### 🥃 

### 🥃 


## Hugging Face AI Agent

- image_generator
- image_captioner
- text_to_speech

| Task                          | Description                                                                                      | Model    |
| ----------------------------- | ------------------------------------------------------------------------------------------------ | -------- |
| Document question answering    | Given a document (such as a PDF) in image format, answer a question on this document              | Donut    |
| Text question answering        | Given a long text and a question, answer the question in the text                                 | Flan-T5  |
| Unconditional image captioning | Caption the image!                                                                               | **BLIP**     |
| Image question answering       | Given an image, answer a question on this image                                                  | VILT     |
| Image segmentation             | Given an image and a prompt, output the segmentation mask of that prompt                         | CLIPSeg  |
| Speech to text                 | Given an audio recording of a person talking, transcribe the speech into text                    | Whisper  |
| Text to speech                 | Convert text to speech                                                                           | **SpeechT5** |
| Zero-shot text classification  | Given a text and a list of labels, identify to which label the text corresponds the most         | BART     |
| Text summarization             | Summarize a long text in one or a few sentences                                                  | BART     |
| Translation                    | Translate the text into a given language                                                         | NLLB     |
