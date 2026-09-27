# Reported Experimental Results

These CSV files transcribe selected tables from the revised TKDE submission supplied by the authors. They are not raw predictions, new experiments, independent reproductions, or a transcription of the earlier arXiv version.

| File | Source | Coverage |
| :--- | :--- | :--- |
| `ser_retrieval.csv` | Table I, manuscript page 7 | Uni-Retrieval and Uni-RAG; five query-to-image settings; R@1 and R@5 |
| `zero_shot_retrieval.csv` | Table IV, page 8 | Uni-Retrieval and Uni-RAG on four additional datasets; R@1 and R@5 |
| `ser_multi_query.csv` | Table VI, page 8 | Uni-Retrieval and Uni-RAG; single- and multi-query retrieval |
| `generation_quality.csv` | Table XI, page 11 | All three reported methods and all six displayed generation metrics |
| `educator_ratings.csv` | Table XII, page 11 | All three reported methods and all four displayed rating columns |

Recall and hallucination columns are percentages on a 0–100 scale. Generation ratings and educator ratings use a 1–5 scale. ROUGE-L uses a 0–1 scale. Empty CSV fields represent settings not reported in the source table, not zero scores.

The `overall_as_reported_1_to_5` column preserves the source value. It is not calculated from the other three educator-rating columns. Counts of 500 refer to evaluated samples or rated answers, not the number of educators.

No uncertainty intervals, significance tests, or statistical claims have been added. The original tables contain additional retrieval baselines; these CSVs intentionally contain only the predecessor/proposed-model comparisons except where all three generation baselines are included.

For Table VI, `style_image` follows the manuscript prose describing T+S as text plus a style-image query; no more specific fixed style is assumed here.
