# Security policy

AnimeRoyale is a personal software project. This policy does not promise continuous monitoring, a response deadline, a security audit, or support for a particular release line. The supplied manifest identifies version `0.1.0`; that alone does not establish a supported-version programme.

## Reporting a vulnerability

Do not publish exploit details, credentials, personal data, or affected users' information in issues or pull requests.

If GitHub private vulnerability reporting is enabled for this repository, use **Report a vulnerability** in its security area. This document does not enable that feature or confirm its availability.

If private reporting is unavailable, open an issue asking only for a private security contact. Do not include the vulnerability itself. No dedicated security email address is established in this repository.

Once a private channel is available, include:

- The affected file, endpoint, and commit or version, if known.
- A description of the issue and its potential impact.
- Minimal reproduction steps using accounts and data you control.
- Redacted evidence and any suggested mitigation.

Avoid destructive testing and unnecessary provider requests. Do not test against other people's deployments without permission. Report exposed credentials privately and rotate credentials you control.

[GitHub private-reporting guidance](https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/report-privately)

## Deployment considerations

Review these source-level limitations before exposing an instance publicly:

- `api/roast.js` has no application-level authentication or rate limiting and accepts client-supplied profile fields before invoking Groq.
- Profile fields enter model prompts. Treat submitted fields and generated output as untrusted; the current implementation does not establish prompt-injection resistance.
- `api/leaderboard.js` accepts unauthenticated client-supplied scores and imports an undeclared Redis dependency. Its scores are not authoritative.
- Handlers return caught error messages. Review whether infrastructure or provider details could be exposed.
- Several dependencies use `latest`, and the supplied snapshot contains no lockfile.

These observations are source-review findings, not a penetration test or a complete security assessment. Documentation changes do not remediate them.

## Secrets and data

Keep `GROQ_API_KEY` and Redis credentials in server-side environment configuration, never client code or reports. Do not expose them through frontend environment variables.

Public profile data is fetched in the browser. Normalised profiles are sent to `/api/roast`; selected fields are forwarded to Groq. Recent results remain in browser local storage until cleared or removed by the browser. External-service retention and infrastructure logging are outside the guarantees made by this policy.
