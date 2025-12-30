# KCD_SNOMED-CT_Automatic_Mapping_Candidate_Recommendation
Internship toy project at Miso Healthcare Division, Miso Information Technology, focusing on KCD–SNOMED CT mapping.
Overview

This project implements an automatic candidate recommendation system that maps
KCD (Korean Classification of Diseases) disease names to SNOMED CT concepts.

Instead of forcing a single predicted label, the system is designed as a
ranking-based recommendation framework, providing Top-K SNOMED CT candidates
to support expert validation and large-scale medical terminology mapping.

Motivation

KCD is widely used in Korea for administrative and statistical purposes

SNOMED CT is an international, concept-based clinical terminology standard

Manual KCD–SNOMED CT mapping:

Requires domain experts

Is time-consuming and costly

Is difficult to maintain at scale

This project addresses these challenges by introducing an AI-assisted, expert-in-the-loop mapping pipeline.

Dataset

SNOMED CT Release Files

Concept

Description (FSN / Preferred Term / Synonym)

Relationship (is-a, attribute relations)

KCD Disease Names

Goldset (A–D)

Expert-validated mapping dataset for evaluation

Preprocessing Strategy (SNOMED CT Target-Centered)

SNOMED CT Release files are structurally complex and not directly suitable for
search, embedding, or similarity comparison.

Therefore, preprocessing is applied to the SNOMED CT terminology set (target),
while KCD disease names are treated as queries with minimal normalization.

Preprocessing Steps

Step 1. Common Normalization

Integrate distributed Release tables

Remove domain-irrelevant tokens

Remove parentheses and auxiliary descriptions

Step 2. SNOMED CT–Specific Rules

Filter concepts using semantic tags
(e.g., disorder, finding, procedure)

Preserve term structure:

FSN

Preferred Term

Synonym

Preserve medical modifiers
(acute/chronic, benign/malignant, etc.)

Step 3. Extended Utilization

Incorporate concept relationships (is-a, attributes)

Support query relaxation strategies

Prepare structured inputs for embedding and re-ranking

Model Architecture
Semantic Embedding

SapBERT

Medical domain–specific BERT model

Trained on UMLS synonym pairs

Used for semantic similarity search

Lexical Features

TF-IDF

Captures exact term overlap and lexical similarity

Complements semantic embeddings

Mapping Pipeline
[Input]
KCD Disease Name (Query)
        |
        v
[Parallel Processing]
- Text normalization
- SapBERT embedding → Semantic similarity
- TF-IDF feature extraction → Lexical similarity
        |
        v
[Hybrid Similarity]
Final Score = α·Semantic + (1−α)·Lexical  (α = 0.7)
        |
        v
[Candidate Retrieval]
FAISS-based Top-K SNOMED CT candidates
        |
        v
[Re-ranking (Heuristic-based)]
- FSN priority (+0.05)
- Length similarity adjustment (≤ +0.03)
        |
        v
[Output]
Top-1 / Top-5 / Top-10 SNOMED CT candidates

Evaluation Metrics

Ranking-based evaluation metrics are used instead of single-label accuracy:

Accuracy@K

Mean Reciprocal Rank (MRR)

NDCG@K

MAP@K

Average Rank

Results

Accuracy consistently increases as K increases

Top-10 Accuracy ≥ 70%

MRR ≈ 0.60, indicating correct concepts are typically ranked within top 2–3

Results confirm suitability of ranking-based recommendation over strict classification

Error Analysis

Error cases show that:

The model captures semantic similarity effectively

Misrankings often arise from SNOMED CT hierarchical structure
(upper-level vs. lower-level concepts)

Errors are structural rather than semantic failures

This supports the use of heuristic re-ranking rather than additional model training.

Key Contributions

Reformulates KCD–SNOMED CT mapping as a recommendation problem

Introduces a hybrid semantic + lexical similarity framework

Applies SNOMED CT–targeted preprocessing for stable concept indexing

Demonstrates feasibility of expert-assisted large-scale mapping

Notes

This system is designed as an expert-support tool, not a fully automated decision system

Final validation by medical professionals is assumed
