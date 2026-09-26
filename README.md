# TasteTether — Explainable Music Recommender

TasteTether is a small, explainable content-based music recommender built for CodePath AI110. It ranks songs against one listener's taste profile using hand-set weights for genre, mood, energy, valence, danceability, acousticness, and tempo. Each recommendation includes the reasons behind its score.

This project is an early step in my recommender work. I later built [Cadence](https://github.com/kyle-m-mitchell/cadence-ai-music-companion), which adds natural-language requests, retrieval, a larger catalog, evaluation, a Streamlit interface, and optional AI features. This simulation remains useful because its scoring rules and limitations are small enough to inspect end to end.

## What I changed

- Expanded the starter catalog from 10 to 20 fictional songs, covering 17 genres and 16 moods.
- Added partial-credit families for related genres and moods, case-insensitive matching, and tempo as a scored feature.
- Tuned the weights after an adversarial profile exposed an off-genre result: an exact genre match now earns 4.0 points, more than the maximum 3.5 points available from mood and numeric features combined. That guarantees an exact-genre song outranks an unrelated-genre song.
- Added readable score explanations and documented design decisions, experiments, and risks in the [model card](model_card.md) and [design diagram](design.mmd).

This is a deterministic scoring simulation. It does not train a model, use listener histories, or call an AI service.

## Try it

From the repository root, use Python 3.10 or newer:

```bash
python3 -m src.main
```

The built-in study profile ranks a 20-song CSV catalog and prints the top five with each feature's score contribution. For example, the current top result is **Midnight Coding** by LoRoom, followed by **Library Rain**. To change the demonstration profile, edit `taste_profile` in [`src/main.py`](src/main.py).

Run the existing tests with:

```bash
python3 -m pytest -q
```

The recommender itself uses Python's standard library. `pytest` is needed only to run the tests.

## How scoring works

For each song, the recommender adds weighted matches. Genre and mood receive full credit for an exact match and half credit for a related family. Numeric features reward closeness to the requested value; tempo is normalized from beats per minute before comparison. The songs are then sorted by score, with song ID breaking ties.

| Feature | Maximum contribution |
| --- | ---: |
| Genre | 4.00 |
| Mood | 1.50 |
| Energy, valence, danceability, acousticness, and tempo combined | 2.00 |

The strong genre weight is an intentional, testable choice. It also has a cost: a listener whose preferred genre has few songs may receive weak filler results after the exact matches run out. The catalog is small and fictional, and hand-authored genre families reflect subjective judgments. An empty or wholly mismatched profile still returns songs in ID order because every score is zero. The [model card](model_card.md) explores these limitations and the evaluation examples in detail.

## Project origin

This repository is a fork of [CodePath's Music Recommender Simulation starter](https://github.com/codepath/ai110-module3show-musicrecommendersimulation-starter). CodePath supplied the initial exercise and catalog. The expanded data, scoring changes, analysis, and documentation in this fork record my work on TasteTether. Cadence is the later, broader application; this repo remains a compact record of the decisions that led to it.
