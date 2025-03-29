# ChatGPT Settings and Operations Guide

## 📖 General Overview

- **Model**: OpenAI's GPT-4
- **Mode**: Business Plan Sage
- **Capabilities**: 
  - Text generation and comprehension
  - Image generation (via DALL-E)
  - Code execution (Python)
  - Web browsing (text-only, no external API calls)

## 📖 User Interaction

- **Language**: Primarily English (multi-language support available)
- **Response Format**: Textual responses, code outputs, and image generation
- **User Queries Handling**: 
  - Informational: Providing explanations, summaries, and clarifications
  - Creative: Assisting in creative writing, idea generation, and problem-solving
  - Technical: Code writing, debugging, and explaining programming concepts

## 📖 Special Features

### DALL-E Image Generation

- Generates images based on descriptive prompts
- Abides by specific content policies (e.g., no generation of copyrighted characters or public figures)

### Python Environment

- Executes Python code for data analysis, mathematical calculations, and more
- Stateful interaction allowing continuation of previous code context

### Web Browsing

- Searches the web for information (text-based)
- Quotes and summarizes content from web pages
- No direct access to URLs outside the search tool

## 📖 Limitations

- **Content Policy**: Adherence to content guidelines (e.g., no offensive content, respect copyright laws)
- **Data Access**: No access to real-time data or external databases
- **Internet Access**: Limited to the internal browser tool

## 📖 Updates

- **Knowledge Cutoff**: April 2023
- Information beyond this date is not included in the model's training data
- Regular updates and improvements to model capabilities and policy guidelines

## 📖 Training

1. 使用javaScript编写一个贪吃蛇游戏
2. 树上有10只鸟，猎人开枪打死一只，请问树上还剩几只鸟？
3. 安德鲁从上午11点到下午3点有空，珍妮中午到下午2点和下午3:30到5点有空。汉娜中午半小时有空，然后是下午4点到6点有空。安德鲁，珍妮和汉娜开会的起始时间选项是什么？



## 📖 Collection (2024-08-31)

(Q) I have many comic images, I want to train images based on these samples. 

- PIL: Python Imaging Library, OpenCV
- re-labeled: Labelbox, Adobe XD
- `TensorFlow`, `PyTorch`: Libraries like `albumentations` or TensorFlow's `ImageDataGenerator` can help.
- `Hugging Face`:Offers pre-trained models and tools for fine-tuning.

(Q) Model Architecture:

- Select a suitable generative model (e.g., `GANs`, `VAEs`, or diffusion models)
- Popular choices include `StyleGAN`, `DCGAN`, `Hugging Face's Diffusers`, or `stable diffusion` models

(Q) Set Up Environment: 

Install necessary libraries, set up the GPU environment if available, and prepare your training scripts.

(Q) Use a Pre-trained Model: 

Fine-tuning a pre-trained model (transfer learning) often yields better results, especially if you have a small dataset. Choose a model pre-trained on a similar domain if possible.

(Q) Define Hyperparameters: 

Set learning rate, batch size, number of epochs, and other hyperparameters.

(Q) Export the Model: 

Save the trained model in a format suitable for deployment (e.g., TensorFlow SavedModel, ONNX).

(Q) Tools and Resources:

- `Hugging Face`: For model fine-tuning and deploying with ease.
- `Google Colab`: For running experiments on GPUs without local hardware.
- `Paperspace Gradient`: For more scalable cloud training with GPUs.
