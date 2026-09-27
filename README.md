# TKDE: Uni-RAG
This is the projects with prof. Erik Cambria in NTU and prof.  Feng-Kuang Chiang in SJTU.

# From Query to Explanation: Uni-RAG for Multimodal Retrieval-Augmented Learning in STEM
If you like our project, please give us a star ⭐ on GitHub for the latest update.

> **Authors **: Xinyi Wu, Yanhao Jia, Luwei Xiao, Shuai Zhao, Fengkuang Chiang, Erik Cambria  
> **Submitted to**: IEEE Transactions on Knowledge and Data Engineering (TKDE), 07/2025   **Major revision**: IEEE TKDE, 08/2026
> 
[![arXiv](https://img.shields.io/badge/arXiv-2507.15061-b31b1b.svg)](https://arxiv.org/abs/2507.03868)
> 
## 1. Introduction

AI-facilitated teaching (AI4EDU) leverages advanced AI to enhance instructional design, learning processes, and assessment across diverse educational settings. Traditional retrieval systems—optimized for natural text–image matching—struggle with the explosion of interdisciplinary STEM resources (diagrams, simulations, multimedia), leading to imprecise or biased results when faced with noisy inputs like sketches or audio. There is a growing need for retrieval algorithms that balance modality adaptability, semantic generalization, and computational efficiency in educational scenarios.

![Figure 1: Uni-RAG framework](images/Figure1.png)
Fig. 1. This advancement provides a scalable and precise solution for diverse educational needs. Previous retrieval models focus on text-query retrieval data or simple image-text retrieval. Our style-diversified retrieval setting accommodates the various query styles preferred by real educational content.

## 2. Motivation 

The motivation stems from three key challenges:
	1.	Diverse Query Modalities: Existing systems cannot flexibly handle low-res sketches, audio descriptions, or domain-specific diagrams.
	2.	Semantic Ambiguity: Noisy or imprecise inputs degrade retrieval accuracy, hindering access to pedagogically valuable resources.
	3.	Efficiency Constraints: Educational applications often demand low latency and resource efficiency to be practical in real-time learning environments.

## 3. Methods 

The Uni-RAG model comprises five core submodules :
	1.	**Prototype Learning Module**: Maps diverse inputs (text, various image styles, audio) into a shared embedding space to extract style-aware prototypes.
	2.	**Prompt Bank with MoE-LoRA**: Stores a bank of prompt tokens and adapts them via a Mixture-of-Experts Low-Rank Adapter, enabling dynamic, style-specific prompt generation.
	3.	**Feature Extractor**: Leverages frozen vision-language encoders (e.g., OpenCLIP) and optional audio-to-text conversion (e.g., GPT-4o) to produce multimodal embeddings.
	4.	**Retrieval-Augmented Generation (RAG)**: Combines adapted prompts with top-k retrieved evidence to form structured inputs for the language model, ensuring grounded and context-aware generation.
	5.	**Training & Inference**: Optimizes only the Prompt Bank parameters (≤50M activated), preserving efficiency; during inference, precomputes query embeddings for fast online retrieval.

![Figure 2: Uni-RAG ](images/Figure2.png)
Fig. 2. The Uni-RAG model’s architechture. Shared prompt tokens are extracted from the Prompt Bank and fed into the input of the feature encoder. Each entry in the Prompt Bank is associated with multiple experts, enabling the representation of diverse style features. After retrieving the top-k relevant items, Uni-RAG concatenates the system prompt with the retrieved content and passes it to the LLM to generate the final explanation for the query.

## 4. Contribution 

1. Unified Multi-style Retrieval and Generation: Introduces a query-prototype-driven RAG framework for STEM scenarios for the first time;
2. Scalable Prompt Bank: Employs a MoE-LoRA mechanism, enabling the retriever to adapt to inputs in new styles;
3. Empirical Validation: Demonstrates superior performance over baselines on datasets such as SER in terms of R@1/R@5, generation quality, and inference speed.

## 5.Experiments 

On the STEM Education Retrieval (SER) benchmark, Uni-RAG significantly outperforms baselines across diverse query styles (text, sketch, art, low-res, audio). Key findings include  ￼:
	•	Retrieval Accuracy: Uni-RAG achieves the highest R@1/R@5 scores (e.g., +12.7% R@1 over fine-tuned CLIP in Text→Image).
	•	Plug-and-Play Flexibility: Prompt Bank structure allows seamless enhancement of other multimodal models.
	•	Efficiency: Only adds ~11 ms per search iteration compared to Uni-Retrieval, while maintaining superior performance .



![Figure 3: Uni-RAG example模型应用案例](images/Figure3.png)
Fig. 3. The case study for our Uni-RAG with the FreestyleRet baseline involves examples from diverse disciplines within Science, Technology, Engineering,
and Mathematics.
图 3. 我们采用 FreestyleRet 基线的 Uni-RAG 案例研究涉及科学、技术、工程和数学等不同学科的示例。
