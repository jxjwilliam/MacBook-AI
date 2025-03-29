## Deepseek R1

uses chain of thought reasoning, reinforcement learning, and expert architectures to achieve top-tier performance efficiently. 🚀

- Reasoning Model
- thinking
- Chain of Thoughts (CoT)
- **Reinforcement Learning** (RL): reward
- **Supervised FineTuning** (SFT)
- Distilled models: Llama, Qwen
- model translation: from MoE(R1-zero) to tranditional transfomer (Llama).
- MoE
- Multi-head latent Attention
- R1 built on top of R1-Zero

### RLHF

Reinforcement Learning from Human Feedback (RLHF) is a machine learning technique that trains an AI agent using direct human feedback to optimize its performance

## V3

`git clone https://huggingface.co/deepseek-ai/DeepSeek-V3-Base`

## 🥃 蒸馏 vs 量化

- 清晰的知识结构图谱
- MoE: 37B/每个模块，20分之一。
- MHLA: Multi Head Latent Attention（多头潜在注意力）

模型压缩的4大方法：

- 蒸馏
- 量化
- 剪枝
- 二值化

## DeepSeek GRPO

- Group Relative Policy Optimization
- Unsloth


## DeepSeek SFT

- hugginface, modelscope download deepseek.
- deepseek-r1 7b，24GB VRAM
- deepseek-r1-distill-qwen-7b
- 公司里不需要量化 （4090）
- qwen-7b-Instruct 模型
- deepseek 蒸馏 qwen-7b的模型

## LatentSync
