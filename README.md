# AnimeRoyale

**Your media stats. An egg-court verdict.**

AnimeRoyale turns public AniList and MyAnimeList profiles into playful inspections, head-to-head battles, and compatibility commentary. Built with React and Vercel Functions, it combines platform-specific data adapters, local scoring rules, and optional AI commentary through Groq.

This is an entertainment project, not an objective measure of taste, expertise, or real-world compatibility.

## Features

- **Solo inspection:** profile statistics, a custom battle score, egg tier, and commentary.
- **Profile battles:** compare two users on the selected platform. Local scoring selects the displayed winner; AI supplies commentary.
- **Matchmaker:** playful comparisons of two profiles' tracking statistics.
- **Media scopes:** anime, manga, novels, or combined activity. Novel-only mode is available for AniList, not MyAnimeList.
- **Commentary styles:** default, tsundere, elitist critic, and shonen protagonist.
- **Sharing:** copy commentary or export a result card as a PNG.
- **Recent results:** reopen or clear up to ten results saved in browser local storage.
- **Fallback commentary:** built-in text when AI requests fail. Profile retrieval still needs network access.

## How it works

1. Fetch public profile statistics directly from AniList GraphQL or Jikan REST.
2. Normalise provider responses into a shared profile shape.
3. Calculate battle scores and egg tiers in the browser.
4. Request optional commentary through `/api/roast`, which calls Groq server-side.
5. Display the result and save it in browser history.

The **battle score** is calculated locally. The **egg tier** uses activity thresholds and special-case rules. The **Egg Score** in solo commentary is model-generated. These are separate values, not interchangeable rankings.

Provider rating scales are not normalised by the current adapters. AniList novel statistics come from its manga-format breakdown, so combined scoring can overlap those aggregates. Treat the output as a game rather than a fair cross-platform benchmark. See [architecture and scoring](docs/architecture.md).

## Stack

| Layer | Implementation |
| --- | --- |
| Interface | React, JavaScript, Vite |
| Styling | Tailwind CSS, Fraunces, Inter |
| Profile data | AniList GraphQL, Jikan REST |
| Commentary | Groq chat completions through Vercel Functions |
| Image export | `html-to-image` |
| Recent history | Browser `localStorage` |

The commentary handler selects `llama-3.3-70b-versatile` in source. Provider availability has not been verified by this documentation refresh.

## Local development

### Prerequisites

Use Node.js and npm compatible with the dependencies you install. The supplied snapshot uses several `latest` ranges, has no `engines` declaration, and contains no lockfile. It therefore does not establish a reproducible dependency set or a verified minimum Node.js version.

From the repository root:

```sh
npm install
```

### Frontend only

```sh
npm run dev
```

Open the address printed by Vite. This does not run the Vercel API functions. Profile lookup and local scoring can still work; unavailable commentary requests use built-in fallback text.

### Frontend and commentary API

Copy [.env.example](.env.example) to `.env.local` and set your own `GROQ_API_KEY`.

PowerShell:

```powershell
Copy-Item .env.example .env.local
```

POSIX shell:

```sh
cp .env.example .env.local
```

Run the existing script, which uses the project's Vercel CLI dependency:

```sh
npm run vercel-dev
```

Complete any required Vercel login or project setup and use the address printed by the CLI. Never commit real credentials.

[Vercel local-development reference](https://vercel.com/docs/cli/dev)

### Checks and build

```sh
npm run lint
npm run build
npm run preview
```

`preview` serves the frontend build, not the API functions. No automated test script is defined in the supplied manifest. These are available commands, not a claim that they have passed.

## Repository map

| Path | Responsibility |
| --- | --- |
| [`src/features/animeRoyale/`](src/features/animeRoyale/) | Form, results, request orchestration, and history |
| [`src/lib/animeApi.js`](src/lib/animeApi.js) | Provider adapters and battle scoring |
| [`src/lib/battle.js`](src/lib/battle.js) | Winner selection, commentary requests, and fallbacks |
| [`src/lib/eggTiers.js`](src/lib/eggTiers.js) | Tier thresholds and formatting |
| [`src/components/`](src/components/) | Shared visual components |
| [`api/roast.js`](api/roast.js) | Server-side Groq integration |
| [`api/leaderboard.js`](api/leaderboard.js) | Separate, incomplete Redis endpoint; not connected to the UI |

## Current limitations

- The leaderboard imports `@upstash/redis`, which is absent from the supplied package manifest. It may block backend bundling and is not a ready-to-use feature. Its writes trust client-supplied scores.
- The commentary handler accepts client-supplied profile data without application-level authentication or rate limiting. Review provider-cost and abuse controls before public deployment.
- Generated battle commentary can contradict the locally selected winner.
- Profile availability, provider failures, and external rate limits can interrupt lookups.
- Dependency reproducibility and styling compatibility need verification: the Tailwind/PostCSS configuration is paired with an unpinned Tailwind dependency.

These are source-review findings, not runtime or production-audit results. See [maintenance notes](docs/maintenance.md).

## Data and security

The browser fetches public profile data and sends normalised profiles to the commentary endpoint. Selected fields are forwarded to Groq. Recent results, including usernames and commentary, are saved in browser local storage. Clearing history removes that browser's saved history; it does not establish deletion from external services or infrastructure logs.

Read the [security policy](SECURITY.md) before reporting vulnerabilities or deploying publicly.

## Contributing

See [contributing](CONTRIBUTING.md) and the [code of conduct](CODE_OF_CONDUCT.md).

## Licence

[MIT](LICENSE). The existing licence and copyright notice are unchanged. This repository does not establish ownership of third-party service names or externally supplied media.
