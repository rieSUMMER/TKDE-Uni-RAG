# Reported Configuration and Reproducibility Notes

This is a record of settings stated in the submitted TKDE manuscript, not a tested runtime configuration or an independent reproduction. Unspecified or inconsistent details are identified rather than filled in with assumed defaults.

## Core configuration

| Component | Reported setting | Source in submitted manuscript |
| :--- | :--- | :--- |
| Retrieval initialization | OpenCLIP pretrained weights; ViT-Large visual backbone | Sections III-C and IV-A |
| Shared prototype/retrieval space | 768 dimensions | Table II and Section IV-A |
| Image prototype | VGG-Gram features followed by projection | Table II |
| Text prototype | Frozen lightweight T5 encoder followed by a trainable projection; T5-small mentioned in latency discussion | Table II; Sections IV-A and IV-C |
| Audio path used in experiments | Whisper transcription followed by the text pathway | Table II and Section IV-A |
| Prompt Bank entries | 16 | Sections IV-A and IV-C |
| Prompt tokens per entry | 4 | Sections IV-A and IV-C |
| Visual/text tower widths | 1024 / 768 | Table II |
| Base prompt shape | 4 × 1024 | Sections IV-A and IV-C |
| Expert count | 4 | Sections IV-A and IV-C |
| Per-expert LoRA rank | 8 | Section IV-C |
| Prompt initialization | Normal distribution with mean 0 and standard deviation 0.02 | Sections IV-A and IV-C |
| Key initialization | Normal distribution with mean 0 and standard deviation 1; L2-normalized | Section IV-C |
| LoRA B initialization | Zero | Section IV-C |
| Generator | Qwen3-0.6B, frozen | Sections III-E and IV-A |
| Retrieval index | L2-normalized 768-d float32 embeddings; FAISS IndexFlatIP with a NumPy fallback | Section IV-C |
| Training image resolution | 224 × 224 | Section IV-A |
| Reported maximum text length | 40 | Section IV-A |
| Optimizer and learning rate | AdamW; 1e-5 | Section IV-A |
| Schedule | Initial linear warmup followed by cosine decay | Section IV-A |
| Seed | 42 | Section IV-A |
| Reported training hardware | 8 A100 GPUs; batch size 24 per GPU | Section IV-A |

These dimensions serve different purposes: 16 entries, four prompt tokens, and four experts do not specify the number of retrieved documents, the number of selected prompt entries, or the number of active experts per token.

## Training stages: preserve the distinction

The general experimental description reports 20 training epochs. The detailed MoE configuration describes 10 epochs of expert fine-tuning followed by two further epochs after adding low-rank adapters. The manuscript does not unambiguously specify how the 20-epoch statement relates to the 10+2-stage schedule. Do not silently turn these descriptions into a single assumed epoch count.

The general model description says that the foundational encoders are frozen, whereas the detailed recipe allows CLIP LayerNorm parameters to be tuned with the experts in stage 1. The executable release should list trainable parameter groups separately for each stage, including projections, bank keys, prompt tokens, router parameters, expert parameters, adapters, and LayerNorm parameters where applicable. The prototype extractors and Qwen generator are described as frozen.

## Specifications still needed from the implementation

Exact model identifiers and revisions, OpenCLIP pretraining tags, tokenizer-to-tower compatibility, Whisper variant, preprocessing constants, warmup duration, optimizer hyperparameters, loss margin, and loss weighting should be recorded. The GPT-Neo tokenizer discussion should be reconciled with the exact tokenizer used by the released OpenCLIP text tower; the T5 prototype pathway should not be conflated with retrieval-tower tokenization.

The text describes sparse expert routing, but the displayed expert-weighted formulation uses a softmax-weighted sum. Publish the actual expert selection rule, number of activated experts, routing normalization, LoRA scaling, and adapter dropout. Do not invent a top-1 or top-2 gate from the expert count.

The paper specifies retrieval of top-k evidence but does not provide a concrete k for the generation experiment. Also specify the number of selected bank entries, document chunking, evidence ordering, context truncation, textual representation of visual evidence, multi-query fusion, and handling of absent or noisy evidence.

For Qwen3-0.6B, release the exact prompt, model revision, decoding settings, maximum generated tokens, and thinking-mode setting. These are required for fair comparisons against the closed-book and vanilla-RAG baselines.

## Evaluation scope

Table XI reports faithfulness, grounding, usefulness, and pedagogy on a 1–5 scale; hallucination as a percentage of answers; and ROUGE-L in [0, 1]. Do not rename the table's 1–5 grounding rating as an automatic normalized grounding metric. The prose also mentions automatic evidence grounding; its exact definition and implementation need to accompany the evaluation release.

Table XII's “Overall” values are preserved exactly as reported. For Uni-RAG, the displayed overall value is 4.38; it must not be presented as the arithmetic mean of the displayed 4.34, 4.39, and 4.30 dimension means. The aggregation rule and educator count should be documented.

## Efficiency: do not combine different measurement scopes

Table V lists 79 ms for query-to-image retrieval and 71 ms for query-to-text retrieval for Uni-RAG. These are not measurements of completing a generated explanation. Its listed 478M parameter value also does not, on its own, establish a full-pipeline parameter count that includes the separate 0.6B generator.

Table IX is a scalability study on an Apple M1 Pro GPU using synthetic 768-dimensional embeddings and flat search. In that table, “end-to-end” is explicitly defined as query encoding plus retrieval, not query encoding plus retrieval plus generation. Do not mix those results with the training hardware or use them to claim a full RAG response time.

Before publishing performance badges, report retrieval and generation latency separately, hardware, batch size, numerical precision, corpus size, whether audio transcription is timed, output length, total parameters, trainable parameters, and activated parameters. Report approximate-nearest-neighbor results only when the corresponding index configuration has actually been benchmarked.

## Proposed code organization — not included in this release

The original ACL repository contains `train.py`, `test.py`, and modules under `src/models/`, `src/dataset/`, and `src/utils/`. The following is a suggested evolution of that layout, not a statement that these Uni-RAG files already exist:

```text
Uni-RAG/
├── train.py                     # Proposed: explicit training-stage entry point
├── test.py                      # Proposed: retrieval evaluation
├── generate.py                  # Proposed: evidence-conditioned generation
├── evaluate_generation.py       # Proposed: generation evaluation
├── configs/                     # Proposed: versioned experiment configurations
├── src/
│   ├── models/
│   │   ├── model.py             # Adapted retrieval model
│   │   ├── prototype.py         # Image/text prototype pathways
│   │   ├── prompt_bank.py       # Bank keys, prompts, and selection
│   │   └── moe_lora.py          # Experts, routing, and low-rank adaptation
│   ├── rag/
│   │   ├── index.py             # Cached embedding index
│   │   ├── evidence.py          # Evidence representation and assembly
│   │   ├── generator.py         # Frozen Qwen3 interface
│   │   └── pipeline.py          # Retrieval-to-generation coordination
│   ├── dataset/                # Loaders and split validation
│   └── evaluation/             # Retrieval metrics and generation judging
└── tests/                      # Unit and integration tests
```

Migrate or reuse predecessor code only after reviewing the implementation and applicable license files. Do not ship empty entry points as though they can reproduce the reported results.
