# Architecture and scoring

This describes the supplied source, not a verified running deployment.

## Application flow

`src/App.jsx` renders `AnimeRoyalePage`, which owns form options, loading/error state, and the current result. It orchestrates profile retrieval, commentary, history, copying, and image export.

`fetchUserStats` in `src/lib/animeApi.js` selects an adapter:

- AniList: GraphQL user statistics, with novel-only data extracted from the manga `NOVEL` format aggregate.
- MyAnimeList: parallel Jikan statistics and full-profile requests. Novel-only mode is rejected; favourite genres are not populated.

Both adapters produce a shared profile shape with display fields, per-media statistics, selected-scope statistics, egg tier, and battle score.

## Activity and battle score

Anime activity units are episodes watched. Manga and novel activity units are rounded from chapters plus ten times volumes; the novel adapter supplies zero volumes.

Combined mode:

```text
combined activity = anime episodes + manga chapters * 0.35
                  + manga volumes * 6 + novel chapters * 0.45
selected activity = round(combined activity)
entries           = anime entries + manga entries
active entries    = anime active + manga active + novel active
battle score      = round(combined activity + entries * 18
                          + active entries * 12 + mean score * 45)
```

Single-scope mode:

```text
battle score = round(activity units + entries * 20
                     + active entries * 12 + mean score * 45)
```

The combined mean is weighted by anime, manga, and novel entry counts. Provider rating scales are not rescaled. Novel aggregates can overlap manga aggregates, so combined output is not deduplicated consumption.

`getWinner` compares battle scores, then activity units, returning no winner if both comparisons tie.

## Egg tiers and model scores

`getEggTier` uses scope-specific activity thresholds, with special cases evaluated first for a narrow mean-score band, more than 365 anime days, and a manga-heavy profile with no anime entries.

Solo commentary also contains a model-generated Egg Score and verdict. That score is separate from the local battle score and tier. The handler bounds it but does not independently establish its accuracy.

## Commentary boundary

`src/lib/battle.js` posts profiles and options to `/api/roast`. The handler formats selected fields into prompts, requests JSON from Groq, and formats display text. It reads `GROQ_API_KEY` server-side.

Built-in text replaces failed commentary, not failed profile retrieval. The battle header uses the local winner while commentary can use the model-returned winner, so they can disagree.

The handler does not independently retrieve or verify submitted statistics. Its source does not establish authentication, rate limiting, comprehensive request validation, or a complete output schema validator.

## Persistence and sharing

`useHistory` stores up to ten results under `animeroyale_history` in local storage. Reopening restores saved data without refreshing provider statistics. Deduplication compares mode, players, platform, and scope, but not tone.

Copying uses the Clipboard API. PNG export uses `html-to-image` on the result container. Permissions, remote images, and rendering conditions need runtime testing.

## Separate leaderboard

`api/leaderboard.js` reads and writes the Redis sorted set `leaderboard:eggscore`. The supplied frontend does not call it. Its Redis package is undeclared, Redis is constructed before handler configuration checks, and writes trust submitted usernames and scores. It is an incomplete integration, not an implemented UI feature.
