# Deploying Ollama & OpenWebUI with Docker

This repository contains a Docker Compose configuration to deploy [Ollama](https://ollama.ai/) and [Open WebUI](https://github.com/open-webui/open-webui) for running AI models locally.

## Table of Contents
- [Overview](#overview)
- [What is a Model?](#what-is-a-model)
  - [Examples of AI Models](#examples-of-ai-models)
- [Disadvantages of Using Models on Cloud Servers](#disadvantages-of-using-models-on-cloud-servers)
- [Components](#components)
  - [Ollama](#ollama)
  - [OpenWebUI](#openwebui)
- [Available Models](#available-models)
  - [Lightweight Models](#lightweight-models)
  - [Balanced Models](#balanced-models)
  - [High-Performance Models](#high-performance-models)
- [Deploying DeepSeek in Docker](#deploying-deepseek-in-docker)
- [Installation and Setup](#installation-and-setup)
  - [Prerequisites](#prerequisites)
  - [Setup Instructions](#setup-instructions)
  - [Pulling Model](#pulling-model)
  - [Integrating Ollama with OpenWebUI](#integrating-ollama-with-openwebui)
  - [Model Management](#model-management)
- [Troubleshooting](#troubleshooting)
  - [Common Issues](#common-issues)
- [Video Tutorial](#video-tutorial)

## Overview

This Docker Compose setup allows you to run powerful AI language models on your own hardware, giving you privacy, control, and flexibility over your AI interactions. The setup consists of two main components: Ollama (the model server) and Open WebUI (the user interface). But first, let's explore some key concepts.

## What is a Model?
A model in machine learning is a mathematical representation that processes data to make predictions or generate outputs based on learned patterns. Models are trained using vast amounts of data and specialized algorithms.

### Examples of AI Models:
- **[GPT (Generative Pre-trained Transformer)](https://openai.com/research/gpt-4)** – Used for text generation, chatbots, and content creation.
- **[DeepSeek](https://www.deepseek.com/)** – An advanced language model optimized for various NLP tasks.
- **[LLaMA (Large Language Model Meta AI)](https://ai.meta.com/llama/)** – A series of language models developed by Meta.
- **[Stable Diffusion](https://stability.ai/stable-diffusion)** – Used for AI-generated images.

## Disadvantages of Using Models on Cloud Servers:
- **Privacy Risks**: Your data is processed on third-party servers, raising privacy concerns.
- **Latency Issues**: Cloud-based models introduce delays, especially with slow internet connections.
- **Cost**: Running models on cloud services can be expensive due to API call charges.
- **Limited Control**: You depend on service availability and policies of cloud providers.

## Components

### Ollama

[Ollama](https://ollama.ai/) is a lightweight, efficient way to run LLMs (Large Language Models) locally on your machine. It provides an easy-to-use interface for deploying models without requiring complex configurations.

### OpenWebUI
[Open WebUI](https://github.com/open-webui/open-webui) is a user-friendly web interface designed to interact with AI models deployed locally. It provides a dashboard where users can chat with models and manage settings conveniently.

## Available Models

Ollama supports a variety of models with different capabilities and resource requirements:

### Lightweight Models
- **[Phi-2](https://huggingface.co/microsoft/phi-2)**: Microsoft's 2.7B compact model (2GB VRAM)
- **[Gemma-2B](https://huggingface.co/google/gemma-2b)**: Google's efficient 2B parameter model (2GB VRAM)
- **[Neural Chat 7B](https://huggingface.co/Intel/neural-chat-7b-v3-1)**: Lightweight, instruction-tuned model (6GB VRAM)
- **[Mistral 7B](https://huggingface.co/mistralai/Mistral-7B-v0.1)**: High-performance 7B model (6GB VRAM)

### Balanced Models
- **[Llama 2 13B](https://ai.meta.com/llama/get-started/)**: Meta's balanced model (10GB VRAM)
- **[Vicuna 13B](https://huggingface.co/lmsys/vicuna-13b-v1.5)**: Berkeley's tuned chatbot (10GB VRAM)
- **[Orca 2 13B](https://huggingface.co/microsoft/Orca-2-13b)**: Microsoft's reasoning-focused model (10GB VRAM)
- **[DeepSeek Coder 6.7B](https://github.com/deepseek-ai/DeepSeek-Coder)**: Code-specialized model (6GB VRAM)

### High-Performance Models
- **[Llama 2 70B](https://ai.meta.com/llama/get-started/)**: Meta's high-end model (48GB VRAM)
- **[DeepSeek 67B](https://huggingface.co/deepseek-ai/deepseek-llm-67b-chat)**: Comprehensive knowledge model (45GB VRAM)
- **[WizardCoder 34B](https://huggingface.co/WizardLM/WizardCoder-Python-34B-V1.0)**: Advanced coding assistant (24GB VRAM)
- **[Mixtral 8x7B](https://huggingface.co/mistralai/Mixtral-8x7B-v0.1)**: Mixture-of-experts model (24GB VRAM)

After downloading Ollama, if you want to run a model without Docker, use the command:
```bash
ollama run modelname
```

For example:
```bash
ollama run mistral
```

## Deploying DeepSeek in Docker
DeepSeek is a powerful AI model that we're going to deploy locally using Ollama.

## Installation and Setup

### Prerequisites
- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) installed
- Sufficient disk space for models (5GB-50GB per model)
- Adequate RAM and VRAM depending on model size
- Linux, macOS, or Windows with [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install)

### Setup Instructions

1. Create a `docker-compose.yml` file with the following content:

```yaml
services:
  ollama:
    image: ollama/ollama
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    restart: always

  openwebui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: openwebui
    ports:
      - "4000:8080"
    volumes:
      - openwebui_data:/app/backend/data
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
    depends_on:
      - ollama
    restart: always

volumes:
  ollama_data:
  openwebui_data:
```

2. Start the services:
```bash
docker-compose up -d
```

### Pulling Model

After downloading the Ollama and OpenWebUI images, enter this command:
```bash
docker exec -it ollama sh
```
Now you are inside the container filesystem. Enter this command:
```bash
ollama run modelname
```
In our case, we are deploying DeepSeek-R1 trained on 1.5B parameters, so the command is:
```bash
ollama run deepseek-r1
```
You can find all the listed models on the [Ollama model library](https://ollama.ai/library). Once you download the model.

### Integrating Ollama with OpenWebUI
Since OpenWebUI is configured to use `OLLAMA_BASE_URL=http://ollama:11434`, it automatically connects to Ollama. To ensure integration:
1. Open OpenWebUI at `http://localhost:4000`.
2. Navigate to the model selection and verify the models available in Ollama.
3. Start chatting with the AI model through OpenWebUI.

### Model Management
- List available models:
```bash
docker exec -it ollama ollama list
```

- Remove a model:
```bash
docker exec -it ollama ollama rm modelname
```

## Troubleshooting

### Common Issues

#### Cannot connect to Ollama API
- Ensure the Ollama container is running: `docker ps`
- Check Ollama logs: `docker logs ollama`
- Verify network configuration in docker-compose.yml

#### Model downloading too slowly
- Check your internet connection
- Try a smaller model first

#### Open WebUI not connecting to Ollama
- Ensure the `OLLAMA_BASE_URL` environment variable is correctly set
- Check that the Ollama service is running and healthy
- Verify network connectivity between containers with `docker network inspect`

## Video Tutorial
If you want to see a hands-on practical demonstration of this setup, check out my YouTube channel: [DevMastery](https://www.youtube.com/watch?v=0pp-hg22MF8)!

---
This guide provides everything you need to set up Ollama and OpenWebUI locally using Docker. Deploying AI models locally ensures privacy, cost savings, and better performance. Happy coding!
