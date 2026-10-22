# Maintenance notes

## Repository identity

Recommendation: keep the slug `animeroyale` and display name **AnimeRoyale**. The existing name is distinctive, matches the interface, and covers solo analysis and battles. A rename would add churn without materially improving clarity.

Suggested GitHub description:

> Playful anime and manga profile analysis with AniList, MyAnimeList, local scoring, and optional AI commentary. Built with React and Vercel Functions.

Suggested topics: `react`, `javascript`, `vite`, `tailwindcss`, `anilist`, `myanimelist`, `groq`, `vercel`, `anime`.

These are suggestions only. Remote name, description, topics, and repository settings have not been changed. No live-demo URL was supplied or verified, so none is advertised.

## Applying this refresh

Extract the ZIP contents into the existing repository root and replace matching files. This is an overlay, not a complete repository. Keep existing source, binary assets, lockfiles, local configuration, and Git history. Do not delete the original repository or replace it with only this archive.

The refresh rewrites README, contributing, security, and conduct documentation; adds an environment template, architecture notes, and a pull-request template; and permits the credential-free environment template through `.gitignore`.

It removes broken machine-specific links, placeholder reporting addresses, unsupported security response/support promises, decorative emoji, and overstated claims. The MIT licence, package manifest, application source, dependencies, and deployment configuration are unchanged.

Review the diff before committing. To revert, restore replaced files and remove newly added files through your normal version-control workflow.

## Findings to address separately

1. **Leaderboard packaging and trust:** `@upstash/redis` is imported but undeclared. Redis is constructed before missing-configuration checks. The UI does not call this endpoint, and writes trust client-supplied scores without authentication. Decide whether to remove the unused endpoint or complete it before deployment.
2. **Dependency reproducibility:** no lockfile is included in the snapshot; several ranges use `latest`. Establish a known-working dependency set and commit its lockfile. Do not infer exact installed versions from this export.
3. **Styling compatibility:** verify the installed Tailwind version against the PostCSS configuration and CSS directives before choosing a fix.
4. **API abuse and errors:** review request limits, provider-cost protection, validation, and safe error responses. The commentary handler has no application-level authentication or rate limiting.
5. **Scoring interpretation:** provider rating scales are not normalised; manga and novel aggregates can overlap. Clarify intended semantics before changing formulas.
6. **Commentary consistency:** model-returned winners can disagree with the deterministic winner shown in the header.
7. **History robustness:** stored JSON is parsed without shape validation; deduplication omits tone; reopened results are not refreshed.

These are source-review findings, not proof of exploitation or observed runtime failures. They were not bundled into a documentation refresh.

## Verification boundary

The documentation was checked against the 45 text-file entries in the supplied Repomix export. Its directory listing also names `src/assets/hero.png`, but no binary content is included. The ZIP therefore contains only changed and new files, not a reconstructed application.

Package script names, environment-variable names, relative documentation links, and ZIP contents were checked. No dependencies were installed. Lint, build, automated tests, browser checks, provider calls, and production deployment were not performed. This refresh makes no runtime, security, accessibility, or deployment-readiness guarantee.

Before release, resolve packaging/dependency questions, run lint and build in the real repository, and exercise the relevant CONTRIBUTING.md scenarios. GitHub private vulnerability reporting requires separate enablement; SECURITY.md does not enable it.
