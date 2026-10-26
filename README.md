# DSA 4060XA Week 6 – Evaluating Recommender Systems

## Student Details
- **Name:** Hans Onesmo
- **Student ID:** 671463

## Lab Objective
This lab evaluates three recommenders on movie rating data using unseen interactions. It measures rating-prediction error (MAE, RMSE), Top-3 ranking quality (Precision@3, Recall@3, NDCG@3), and catalogue coverage, novelty and diversity. The goal is to decide which model best suits a stated business objective, not just which one is "most accurate".

## Datasets
- **`DSA4060_Week6_Movies.csv`** – 20 movies with `movie_id`, `title` and pipe-separated `genres`.
- **`DSA4060_Week6_Ratings.csv`** – 120 explicit 1–5 ratings from 12 users (10 each), with `user_id`, `movie_id`, `rating` and `timestamp`.

For each user, the most recent rating is held out as test data (12 test ratings) and the other 108 are used for training. A held-out movie counts as relevant if its rating is 4 or 5 (5 of the 12 test items).

## Models Compared
1. **Popularity Baseline** – predicts each movie's average training rating (global mean if unseen); ranks by mean rating, then rating count.
2. **Content-Based Filtering** – genre features, rating-weighted user profile, cosine similarity converted to a 1–5 score.
3. **Item-Based Collaborative Filtering** – cosine similarity between movies from the user–item matrix, with a similarity-weighted average of the user's own ratings.

## Results
| Model | MAE | RMSE | Precision@3 | Recall@3 | NDCG@3 | Coverage | Novelty | Diversity |
|---|---|---|---|---|---|---|---|---|
| Popularity Baseline | 1.086 | 1.201 | 0.067 | 0.20 | 0.10 | 0.35 | 1.689 | 0.736 |
| Content-Based | 1.094 | 1.381 | 0.067 | 0.20 | 0.20 | 0.70 | 1.344 | 0.542 |
| Item-Based CF | **0.924** | **1.070** | 0.000 | 0.00 | 0.00 | **0.85** | **1.695** | **0.780** |

- **Best model for rating prediction:** Item-Based CF (lowest MAE 0.924 and RMSE 1.070), about 15% lower MAE than the baseline. Content-Based did not beat the baseline.
- **Best model for ranking:** Content-Based. It ties the baseline on Precision@3 and Recall@3 but has the highest NDCG@3 (0.20), because its relevant hit ranked first. Item-Based CF found no relevant movies in the Top-3.
- **Coverage, novelty and diversity:** The Popularity Baseline covers only 35% of the catalogue (7 of 20 movies). Content-Based covers 70% but has the lowest novelty (1.34) and diversity (0.54). Item-Based CF has the highest coverage (85%), novelty and diversity, but no relevant hits.
- **Extension (Option A – change K):** Recall@K rises or stays flat as K increases, while Precision@K tends to fall because each user has only one relevant test item. Content-Based stays best across K = 1, 3 and 5.

## Final Recommended Model
**Content-Based Filtering.** For a service that wants relevant discoveries without the same popular titles dominating, it is the only model that matches or beats the baseline on relevance (NDCG@3 0.20, Recall@3 0.20) while covering twice as much of the catalogue (70% vs 35%). The trade-off accepted is lower novelty and diversity, so lists can feel genre-narrow.

## Limitations
1. **Tiny test set:** only 12 held-out ratings and 5 relevant items, so one extra hit changes ranking metrics by 0.2 or more. The model differences are not statistically reliable.
2. **Sparse, small data:** 12 users and 20 movies (10 ratings per user) limit what collaborative filtering and the user profiles can learn.
3. **Genre-only content features:** movies with the same genres look identical, and the user profile rewards genre frequency as well as genuine liking.
4. **Single hold-out and fixed relevance rule:** one most-recent item per user and a rating ≥ 4 threshold give a narrow view of quality. Offline metrics cannot show real user satisfaction.
