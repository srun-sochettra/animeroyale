# Contributing to AnimeRoyale

Keep changes focused on playful media-profile analysis with understandable statistics and reliable interactions. A small, well-explained fix is more useful than unrelated scaffolding.

## Before starting

Read the [README](README.md), [architecture](docs/architecture.md), and [maintenance notes](docs/maintenance.md). Check existing issues and pull requests for overlapping work. Explain the user problem before proposing a large feature or dependency.

Follow the [code of conduct](CODE_OF_CONDUCT.md). Report vulnerabilities through the [security policy](SECURITY.md), not public bug reports.

## Development

Install with `npm install`. Use `npm run dev` for frontend work. For commentary, copy [.env.example](.env.example) to `.env.local`, supply your own Groq key, and run `npm run vercel-dev`.

The supplied snapshot has no lockfile and uses several `latest` ranges. Record your Node.js and installed dependency versions when reporting installation problems. Do not silently fix the separate leaderboard or dependency issues in an unrelated documentation change.

## Change guidelines

- Reuse existing components and adapters before adding new layers.
- Keep provider-specific data handling in `src/lib/animeApi.js`.
- Keep deterministic winner selection separate from generated commentary.
- Preserve loading, error, fallback, and history behaviour unless explicitly changing it.
- Never add credentials, invented metrics, fake badges, or unsupported verification claims.
- Use relative links for repository files, not machine-specific links.
- Check keyboard and touch operation, narrow layouts, and reduced-motion preferences for visual changes.
- Explain scoring changes and their effect on results. Entertainment scores are not objective assessments.

## Verification

Run the existing checks where possible:

```sh
npm run lint
npm run build
```

No automated test script is defined in the supplied manifest. For behaviour changes, add the smallest appropriate regression check and report exactly what ran. A documentation edit alone does not justify a new test framework.

For relevant changes, manually check:

1. Solo, battle, and matchmaker modes with valid public profiles.
2. Both providers, including the unavailable MAL novel-only option.
3. Empty usernames, unknown profiles, failed lookups, and slow responses.
4. Commentary success and fallback when the API is unavailable.
5. Local winner and generated commentary consistency.
6. History reopening, clearing, and unavailable browser storage.
7. Clipboard success/failure and PNG export.
8. Keyboard operation, visible focus, and narrow-screen layout.

Only report scenarios actually exercised. Use your own test data and redact private information.

## Pull requests

Describe the problem, focused change, checks run, and remaining gaps. Include before/after screenshots for visual changes and explain data, API, dependency, or deployment implications. Keep unrelated cleanup separate.
