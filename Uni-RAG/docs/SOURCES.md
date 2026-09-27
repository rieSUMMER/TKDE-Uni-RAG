# Source and Version Notes

## Primary scientific source

**Title:** From Query to Explanation: Uni-RAG for Multimodal Retrieval-Augmented Learning in STEM.  
**Authors:** Xinyi Wu, Yanhao Jia, Luwei Xiao, Shuai Zhao, Fengkuang Chiang, Erik Cambria.  
**Source:** author-provided revised manuscript submitted to TKDE; 13 pages.  
**Supplied filename:** `Highlight_Uni_RAG_on_TKDE (1).pdf`.  
**SHA-256:** `72c5dd8baf00c38cc7fda8db3608e9f07a83946681b2622476b5a1772e681686`.

This manuscript supplies the method descriptions, experimental configurations, figures, result tables, and author contacts used in the documentation. The manuscript itself is not included in the package. Its TKDE submission status is supplied by the author; no acceptance or journal publication date is asserted.

## Verified public resources

Public resources were checked on 2026-09-27.

- [Uni-RAG early preprint](https://arxiv.org/abs/2507.03868): arXiv:2507.03868, first submitted on 2025-07-05. The public page showed v1 at the time of checking. It uses “Multi-Modal” in the title. This is the basis for the preprint citation, not the numerical results copied from the revised TKDE manuscript.
- [Uni-Retrieval repository](https://github.com/CuriseJia/ACL25-Uni-Retrieval): its README and file tree supply predecessor-project context, not evidence that the Uni-RAG implementation has been released.
- [ACL Anthology publication record](https://aclanthology.org/2025.acl-long.502/): authoritative venue, year, page range, and DOI for Uni-Retrieval.
- [ACL paper PDF](https://aclanthology.org/2025.acl-long.502.pdf): the author line names the third author as **Hao Li**. The Anthology's current BibTeX export instead contains `Hao, Li`; the citation included here uses `Li, Hao` to match the paper's byline and the author's name in the Uni-Retrieval arXiv record. This normalization is explicit rather than silently copied from the metadata export.
- [GitHub documentation on citation files](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-citation-files): basis for placing `CITATION.cff` at the repository root and identifying the public preprint as the preferred citation.

## Boundaries of this package

This package is a new documentation scaffold. It does not copy the predecessor source code, introduce or validate a Uni-RAG implementation, upload material to GitHub, provide datasets or checkpoints, reproduce model outputs, or assign a new code/data license.

Proposed directory layouts, release requirements, and deployment precautions are documentation recommendations, not claims about existing released code or additional findings in the paper.
