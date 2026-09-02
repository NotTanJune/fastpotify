# Copilot instructions for Fastpotify

Use `AGENTS.md` and `CONTRIBUTING.md` as the source of truth for every change
and review.

When answering or triaging an issue that asks for a Spotify-facing feature,
consult `docs/_reference/what-spotify-allows.md` first. It lists what the
Web API, the librespot session, and librespot playback each offer, and the
requests none of them can serve, with the reason for each. A request in its
last section (pins synchronised with the Spotify app, editing playlist
folders, Smart Shuffle, lossless audio, local files, playback speed, and
the rest) is answered with that reason, a link to the page, and what the app
offers instead, and then closed; do not propose implementing it. A request
that is under one of the three surfaces but not built is a valid feature
request. If the page has gone stale because librespot or the Web API changed,
say so and ask for it to be updated rather than answering from memory.

When reviewing a pull request, prioritize correctness, regressions, product
fit, cross-platform behaviour, UI-thread blocking, credential exposure, and
unnecessary dependencies. Treat violations of the documented product
boundaries as blockers. In particular, flag alternate sources for Spotify
audio, DRM circumvention, embedded browser engines, telemetry, and hosted
Fastpotify services.

Check that behavioural changes have focused tests and that user-visible
settings, files, and network access are documented. For UI changes, ask for
visual evidence when the pull request does not provide it. Do not spend review
comments on formatting that rustfmt already enforces.

CI passing is necessary but not proof that a change is correct. Give concrete,
actionable findings tied to changed lines; avoid generic summaries and do not
approve or recommend merging code that you cannot substantively evaluate.
