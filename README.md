<div align="center">

# From Query to Explanation: Uni-RAG for Multimodal Retrieval-Augmented Learning in STEM

**Xinyi Wu · Yanhao Jia · Luwei Xiao · Shuai Zhao · Fengkuang Chiang · Erik Cambria**

**Manuscript submitted to IEEE Transactions on Knowledge and Data Engineering (TKDE)**

[![arXiv](https://img.shields.io/badge/arXiv-2507.03868-b31b1b.svg)](https://arxiv.org/abs/2507.03868)
[![ACL 2025](https://img.shields.io/badge/Built%20on-Uni--Retrieval%20%28ACL%202025%29-blue)](https://github.com/CuriseJia/ACL25-Uni-Retrieval)

**Style-diverse queries → Multimodal evidence → Grounded STEM explanations**

[Overview](#overview) · [What Is New](#what-is-new-beyond-uni-retrieval) · [Method](#method) · [Results](#results) · [Availability](#availability) · [Citation](#citation)

</div>

> **Version and release status.** This initial repository release contains project documentation, manuscript figures, and paper-reported results. Uni-RAG-specific training/inference code, checkpoints, and generation-evaluation assets are not included yet. The linked arXiv paper is the **2025 early preprint**; the method details and tables below follow the **revised TKDE submission**, not a reproduction of the early preprint. 

## Overview

​​<img src="https://github.com/rieSUMMER/TKDE-Uni-RAG/blob/main/images/Figure1.png#:~:text=.DS_Store-,%E5%9B%BE,-1.png" width="100%" />​​ 

</div>

> Fig.1.This advancement provides a scalable and precise solution for diverse educational needs. Previous retrieval models focus on text-query retrieval data or simple image-text retrieval. Our style-diversified retrieval setting accommodates the various query styles preferred by real educational content.

**Uni-RAG** is a style-aware multimodal retrieval-augmented generation framework for STEM education. It extends **Uni-Retrieval (ACL 2025)** from retrieving relevant educational resources to generating explanations, feedback, and instructional content grounded in retrieved evidence.

The framework addresses heterogeneous educational queries, including text, natural images, sketches, artistic images, low-resolution visuals, and audio descriptions. A prototype-guided **Prompt Bank with Mixture-of-Experts Low-Rank Adaptation (MoE-LoRA)** adapts retrieval representations to different query styles. Retrieved evidence is then organized with the user query and a system prompt for a frozen **Qwen3-0.6B** generator.

The current system combines **multimodal retrieval with textual generation**. Retrieved images and diagrams can be displayed alongside an answer; generating new diagrams or other visual content is outside the current implementation described in the manuscript.

## What Is New beyond Uni-Retrieval?

Uni-RAG builds on the retrieval framework and SER benchmark introduced in [Uni-Retrieval](https://aclanthology.org/2025.acl-long.502/).

| Aspect | Uni-Retrieval — ACL 2025 | Uni-RAG — TKDE submission |
| :--- | :--- | :--- |
| Main objective | Retrieve educational resources from style-diverse queries | Retrieve evidence and generate grounded educational explanations |
| Prompt adaptation | Prototype-guided Prompt Bank | Prompt Bank extended with MoE-LoRA expert adaptation |
| Generation component | Retrieval-focused framework | Frozen Qwen3-0.6B conditioned on retrieved evidence |
| Output | Ranked retrieval results | Retrieved resources and natural-language explanations, feedback, or instructional content |
| Evaluation scope | Multimodal retrieval | Retrieval, generation quality, hallucination rate, and educator ratings |

The main extensions are **MoE-LoRA prompt adaptation**, **retrieval–generation integration**, and **evaluation of the resulting educational explanations**. SER is the existing benchmark from the ACL work, not a newly introduced dataset in this extension.

## Method

<img src="https://github.com/rieSUMMER/TKDE-Uni-RAG/blob/main/images/Figure2.png#:~:text=Figure1.png-,Figure2,-.png" width="100%" />​​ 

</div>

> Fig.2.The Uni-RAG model’s architechture. Shared prompt tokens are extracted from the Prompt Bank and fed into the input of the feature encoder. Each entry in the Prompt Bank is associated with multiple experts, enabling the representation of diverse style features. After retrieving the top-k relevant items, Uni-RAG concatenates the system prompt with the retrieved content and passes it to the LLM to generate the final explanation for the query.

**1. Query prototypes.** Image queries use VGG-Gram style features; text queries use a lightweight T5 encoder. In the reported experiments, audio is transcribed with Whisper and follows the text pathway. Modality-specific projections map prototypes into a shared 768-dimensional space.

**2. Style-aware prompt adaptation.** Query prototypes select relevant key–prompt entries from the Prompt Bank. MoE-LoRA experts adapt the selected prompts before their insertion into the vision or text encoder. The detailed manuscript configuration uses **16 bank entries**, **four prompt tokens per entry**, **four experts**, and **LoRA rank 8**.

**3. Cross-modal retrieval.** OpenCLIP-based encoders produce aligned retrieval embeddings. Relevant educational items are selected by cosine similarity. The manuscript describes cached, normalized document embeddings and inner-product search with FAISS IndexFlatIP, with a NumPy fallback.

**4. Evidence-conditioned generation.** The generator receives structured context in the form:

```text
[PROMPT: system instruction; EVIDENCE: retrieved materials; QUERY: user query]
```

A frozen Qwen3-0.6B model produces the textual response. Multimodal retrieval should not be interpreted as direct image input to a vision-language generator: this version produces text using the available evidence descriptions and associated textual context.

## Results

### Retrieval on SER

Comparison with the original Uni-Retrieval baseline from **Table I**. R@1 and R@5 are percentages; higher is better.

| Query → Target | Uni-Retrieval R@1 | Uni-RAG R@1 | Uni-Retrieval R@5 | Uni-RAG R@5 |
| :--- | ---: | ---: | ---: | ---: |
| Text → Image | 83.2 | 84.1 | 98.7 | 99.0 |
| Sketch → Image | 84.5 | 85.1 | 95.6 | 98.1 |
| Art → Image | 76.9 | 77.2 | 97.5 | 97.9 |
| Low-resolution → Image | 87.4 | 89.5 | 98.1 | 98.7 |
| Audio → Image | 51.4 | 53.7 | 87.5 | 89.4 |

For multi-query retrieval, **Table VI** reports **88.7% R@1** for text + style image → image, compared with **84.1%** for text → image alone. Here, T+S follows the manuscript's description of text plus a style-image query. These are different query settings, not interchangeable benchmark scores.

### Zero-shot retrieval across additional datasets

Selected results from **Table IV**. Values are R@1 percentages; “—” denotes a setting not reported in the table.

| Dataset | Method | Text → Image | Sketch → Image | Art → Image | Low-resolution → Image |
| :--- | :--- | ---: | ---: | ---: | ---: |
| DSR | Uni-Retrieval | 82.3 | 82.7 | 75.1 | 91.2 |
| DSR | **Uni-RAG** | 84.0 | 84.5 | 77.6 | 91.3 |
| ImageNet-X | Uni-Retrieval | 73.9 | 70.6 | 65.3 | 78.6 |
| ImageNet-X | **Uni-RAG** | 74.4 | 72.3 | 67.0 | 80.5 |
| DomainNet | Uni-Retrieval | 70.7 | 77.6 | 73.4 | — |
| DomainNet | **Uni-RAG** | 71.2 | 79.3 | 75.0 | — |
| SketchCOCO | Uni-Retrieval | 34.7 | 30.2 | — | — |
| SketchCOCO | **Uni-RAG** | 35.0 | 30.6 | — | — |

Dataset names follow the submitted manuscript. Exact versions, split manifests, and preprocessing resources should accompany the implementation release.

### Generation quality

Results on the **500-sample SER generation-evaluation split** from **Table XI**. Faithfulness, grounding, usefulness, and pedagogy use a 1–5 scale; hallucination is the percentage of answers; ROUGE-L lies in [0, 1].

| Method | Faithfulness ↑ | Grounding ↑ | Usefulness ↑ | Pedagogy ↑ | Hallucination (%) ↓ | ROUGE-L ↑ |
| :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| Closed-book Qwen3-0.6B | 3.12 | 2.40 | 3.35 | 3.17 | 39.0 | 0.24 |
| Vanilla RAG: CLIP + Qwen3 | 3.87 | 3.79 | 3.88 | 3.74 | 20.2 | 0.34 |
| **Uni-RAG** | **4.33** | **4.29** | **4.34** | **4.19** | **8.2** | **0.41** |

The manuscript describes fixed-rubric LLM judging with educator cross-validation. Relative to vanilla RAG, the reported hallucination rate decreases from **20.2% to 8.2%**, a reduction of **12.0 percentage points** on this evaluation split.

### Educator ratings

**Table XII** reports the following mean ratings on a 1–5 scale over **500 answers**. “Overall” is transcribed as reported, not recomputed from the displayed dimensions.

| Method | Helpfulness ↑ | Correctness ↑ | Clarity ↑ | Overall ↑ |
| :--- | ---: | ---: | ---: | ---: |
| Closed-book Qwen3-0.6B | 3.24 | 3.18 | 3.61 | 3.31 |
| Vanilla RAG: CLIP + Qwen3 | 3.86 | 3.88 | 3.93 | 3.88 |
| **Uni-RAG** | **4.34** | **4.39** | **4.30** | **4.38** |

These results concern answer quality and educator ratings. They should not be interpreted as evidence of measured improvements in student learning outcomes.

## STEM Examples

<p align="center">
  <img src="https://github.com/rieSUMMER/TKDE-Uni-RAG/blob/main/images/Figure3.png#:~:text=Figure2.png-,Figure3,-.png" width="100%">
</p>

</div>

> Fig.3.illustrates retrieval and explanation examples involving a chemistry experiment, a line-following robot, a tower crane, and derivatives interpreted through tangent lines. These are qualitative examples from the manuscript, not outputs reproduced by a runnable demo in this release.



## Availability

| Resource | Status in this release |
| :--- | :--- |
| Project overview, architecture, and manuscript figures | Included |
| Selected manuscript results in CSV format | Included |
| Citation metadata | Included |
| Original ACL Uni-Retrieval implementation | Available in the [upstream repository](https://github.com/CuriseJia/ACL25-Uni-Retrieval) |
| Uni-RAG-specific training and inference implementation | Pending release |
| Uni-RAG checkpoints and validated environment | Pending release |
| SER access instructions and exact evaluation split manifests | Pending release |
| Generation prompts, judge rubric, references, and evaluation scripts | Pending release |

This repository is currently a **documentation-and-results release**, not an installable implementation. The ACL repository is the predecessor implementation; it should not be treated as the complete Uni-RAG release.

The intended implementation workflow is: prepare datasets → train the adapted retriever → cache evidence embeddings → retrieve relevant evidence → generate explanations → evaluate retrieval and generation separately. Validated installation commands and executable entry points should be published with the corresponding code and checkpoints.

## Scope and Limitations

Audio queries follow a transcription-based pathway in the reported experiments. Performance therefore depends partly on transcription quality; a unified audio encoder remains a direction for future work. The current generator produces text rather than newly synthesized diagrams.

Retrieval-only latency must be distinguished from full retrieval-plus-generation latency. The manuscript's retrieval timing and synthetic embedding scalability measurements do not establish an end-to-end explanation-generation latency.

As a recommended deployment precaution, generated educational content should be reviewed by an appropriately qualified educator, especially when it discusses experiments or engineering safety. Retrieval and educator ratings do not guarantee correctness for every query.

## Acknowledgments

Uni-RAG builds on [Uni-Retrieval](https://github.com/CuriseJia/ACL25-Uni-Retrieval). We acknowledge OpenCLIP, FreestyleRet, Qwen3, Whisper, and FAISS, as well as the creators of the datasets used in the study.

## License

License information for Uni-RAG-specific code, checkpoints, and released data is pending maintainer confirmation. This initial documentation package does not include code, model-weight, or dataset license files. Applicable licensing information should be provided with each corresponding release.

## Citation

Please cite Uni-RAG when building on this work, and also cite Uni-Retrieval when using its retrieval framework or the SER benchmark.

The first entry below identifies the **public 2025 preprint**, whose title uses “Multi-Modal.” The README title and reported experimental results follow the revised TKDE manuscript. This is not a citation to an accepted TKDE article.

```bibtex
@misc{wu2025queryexplanationuniragmultimodal,
  title         = {From Query to Explanation: {Uni-RAG} for Multi-Modal Retrieval-Augmented Learning in {STEM}},
  author        = {Wu, Xinyi and Jia, Yanhao and Xiao, Luwei and Zhao, Shuai and Chiang, Fengkuang and Cambria, Erik},
  year          = {2025},
  eprint        = {2507.03868},
  archivePrefix = {arXiv},
  primaryClass  = {cs.AI},
  doi           = {10.48550/arXiv.2507.03868},
  url           = {https://arxiv.org/abs/2507.03868}
}

@inproceedings{jia2025uniretrieval,
  title     = {{Uni-Retrieval}: A Multi-Style Retrieval Framework for {STEM}'s Education},
  author    = {Jia, Yanhao and Wu, Xinyi and Li, Hao and Zhang, Qinglin and Hu, Yuxiao and Zhao, Shuai and Fan, Wenqi},
  booktitle = {Proceedings of the 63rd Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)},
  year      = {2025},
  publisher = {Association for Computational Linguistics},
  address   = {Vienna, Austria},
  pages     = {10182--10197},
  doi       = {10.18653/v1/2025.acl-long.502},
  url       = {https://aclanthology.org/2025.acl-long.502/}
}
```

See [CITATION.bib](CITATION.bib) and [CITATION.cff](CITATION.cff) for machine-readable citations. ACL author names follow the published paper PDF; a metadata discrepancy is documented in [source notes](docs/SOURCES.md).

## Contact

For research questions, contact **Xinyi Wu** at [summer.xywu@sjtu.edu.cn](mailto:summer.xywu@sjtu.edu.cn), **Yanhao Jia** at [yanhao002@e.ntu.edu.sg](mailto:yanhao002@e.ntu.edu.sg), or the corresponding author, **Shuai Zhao**, at [shuai.zhao@ntu.edu.sg](mailto:shuai.zhao@ntu.edu.sg).
