# Dataset Preparation and Release Requirements

This document describes the benchmarks named in the submitted TKDE manuscript. It is not a dataset download manifest. No datasets, split IDs, expert references, or access credentials are included in this release.

## Benchmarks in the paper

| Dataset | Role in Uni-RAG | Manuscript location |
| :--- | :--- | :--- |
| SER | Primary STEM retrieval benchmark; also provides the held-out generation-evaluation sub-split | Section IV-A; Tables I, III, VI, XI, XII |
| DSR | Additional zero-shot style-diversified retrieval evaluation | Table IV |
| ImageNet-X | Additional zero-shot retrieval evaluation | Table IV |
| DomainNet | Additional zero-shot retrieval evaluation | Table IV |
| SketchCOCO | Additional zero-shot retrieval evaluation | Table IV |

SER was introduced with Uni-Retrieval (ACL 2025). Consult the [conference paper](https://aclanthology.org/2025.acl-long.502/) and [original repository](https://github.com/CuriseJia/ACL25-Uni-Retrieval) for the predecessor project. This documentation does not establish a currently working SER download endpoint.

The dataset names above are preserved exactly as used in the TKDE manuscript. In particular, do not substitute an unrelated resource merely because it uses the name “ImageNet-X.” The manuscript's “SketchCOCO” name and its reference to SketchyCOCO also require a precise release identifier from the maintainers.

## Required retrieval assets

Before executable instructions are published, provide the authorized data-access route; immutable dataset versions; train/validation/test manifests; query and gallery IDs; positive-match definitions; and the mappings among text, natural images, sketches, art, low-resolution images, and audio. Document whether audio transcripts are precomputed or generated during evaluation.

State whether evaluation uses instance-level positives, category-level positives, or both. Release split construction details and checks against leakage between paired query styles and their gallery items.

For DomainNet and SketchCOCO, the paper describes captions generated with InternVL-1.5 and a quality-control procedure involving constrained prompting, automatic filtering, and sampled human verification. Release the retained captions, generation prompts, filtering thresholds, and applicable redistribution information needed to reproduce those inputs.

## Required generation-evaluation assets

Table XI evaluates 500 samples, and Table XII reports educator ratings over 500 answers. Release the exact sample IDs and evidence corpus snapshot, expert reference answers, retrieved evidence records, full generation prompt, and decoding configuration. Do not assume that the two evaluations use identical answer records without the corresponding manifests.

Also provide the judge model and version, scoring rubric, hallucination definition, judge prompt, educator-rating protocol, number of educators, and aggregation rules. “500 answers” is not the number of educators; the submitted manuscript does not give a numeric value for the educator count in the relevant paragraph.

No synthetic examples, suggested schemas, or placeholder IDs in documentation should be represented as the official SER or RAG-evaluation release.
