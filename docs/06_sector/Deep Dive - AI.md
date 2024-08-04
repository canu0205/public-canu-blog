---
title: A Primer on AI
slug: deep-dive-ai
date: <% tp.date.now("YYYY-MM-DD HH:mm:ss") %>
update: <% tp.date.now("YYYY-MM-DD HH:mm:ss") %>
publish:
---
> [!DISCLAIMER]
> This is based on Deep Dive series of [Chamath Palihapitiya](https://chamath.substack.com/). IF you want to access full contents of this series, you should subscribe his substack.

# Types of Neural Network

Neural Networks are a type of logic system that attempts to replicate the structure of the human brain.

Neurons in the human brain fire together to recalling information, while nodes in the neural network fire together to replicate actions of neurons.

![[Pasted image 20240722150419.png]]

There are several ways of the neural network to learn: supervised learning, unsupervised learning, reinforcement learning.

To improve accuracy of predictions, cost function, which is difference between the model's predictions and the true value based on the labeled training data, should be minimized.

- Supervised learning: input labeled data and get probability of each data as output
- Unsupervised learning: input unstructured & unlabeled data and get grouped data as output
- Reinforcement learning: rewards and punishments to train a neural network

## Convolutional Neural Network(CNN)

Introduced in 1990s to aid w/ complex **image processing and classification tasks**.

![[Pasted image 20240722163111.png]]

## Generative Adversarial Network(GAN)

Using two CNNs, a generator and a discriminator, to generate new images which are **indistinguishable from real photos**.

![[Pasted image 20240722163343.png]]

While GNNs are very effective at generating realistic images, they can be **difficult and unstable to train**.

## Diffusion model

It is a new class of image generation tool that work by gradually adding noise to an image, and then learning to reverse this process to generate new images.

![[Pasted image 20240722164654.png]]

Diffusion models are more computationally intensive than GANs, but often yield **more stable and realistic outputs**.

## Recurrent Neural Network(RNN)

RNNs can cycle info through loops, allowing them to **recall previously processed data in sequence.**

![[Pasted image 20240722165251.png]]
> **Vanishing gradient problem**
> > gradient of cost function approaches zero, the model's weight and biases in early layers of the network aren't updated, so the network stops learning.

## Long-short-term Memory Network(LSTM)

> Variation of RNNs

LSTMs are developed w/ built-in memory cells and 'gates' to filter and remember longer strings of key info.

![[Screenshot 2024-07-22 at 5.07.33 PM.png]]

However, LSTMs require input words to be passed sequentially to capture their position in the context of other words, which is inefficient and cannot take advantage of using GPUs.

## Transformer Model

Used extensively in natural language processing tasks, including translation, text summarization, and question answering.

Consist of two blocks - an encoder and a decoder.

![[Pasted image 20240722172050.png]]

Moreover, encoders and decoders can be separated to perform different tasks.

![[Screenshot 2024-07-22 at 5.22.40 PM.png]]


# Intro to Large Language Models

There are two types of AI models: **the discriminative model and the generative model.**

LLMs are a type of generative model that can write new strings of text using natural language as their input. LLMs must first be trained before they can make predictions about words using a process called inference.

![[Screenshot 2024-07-23 at 3.29.29 PM.png]]

This kinda pre-training requires large amount of compute power to represent and map the relationships between words. GPT-4 was trained using ~25,000 NVIDIA A100 GPUs for ~90-120 days.

Once a LLM is pre-trained, we can fine-tune it to perform a particular task like **answering questions.**

Reinforcement learning from human feedback(RLHF) is used to rank the model's responses.

![[Screenshot 2024-07-23 at 3.34.05 PM.png]]

After fine-tuning, models predict answers to questions using a **process called 'inference'.**
Inference is less computationally intensive than training, but still requires clusters of specialized hardware to compute the appropriate response to a given answer.

There are two key issues during inference: **Hallucination and High level of power & time consumed.**

- Hallucination: output convincing but false answers to given questions since lack of contexts.
- High level of power & time consumed: models use large GPU clusters which draw on significant power to calculate the next most probable word, models generate words token by token - for larger and more complex models, the latency to generate the next token can be frustrating.

One way to overcome hallucination is **Retrieval Augmented Generation(RAG)**, which trains a model to use a dataset of specific knowledge to provide more context-aware responses.

- Mixture of experts: route questions to smaller models that are 'experts' in that field, reducing the time and compute requirements to run inference.

