---
title: What Spotify Lets a Client Do
description: The three Spotify surfaces Fastpotify can reach, what each of them offers, and the requests that fall outside all three.
nav_order: 3
---

Fastpotify is a third-party client. It can only do what Spotify exposes to
one, through three surfaces: the [Web API](https://developer.spotify.com/documentation/web-api),
the session that [librespot](https://github.com/librespot-org/librespot)
maintains, and librespot's own playback. A feature that none of them
carries is not a bug in Fastpotify and not something a pull request can
add; it waits for Spotify, or for lawful support upstream in librespot.

This page is the reference for answering such requests. Maintainers and the
agents that help on the repository should quote it rather than reason from
scratch, and close a request that lands in the last section with the reason
given there, leaving it open only when librespot has since gained the
capability.

## What the Web API gives

Everything the app reads and writes about the account and the catalogue.
Fastpotify uses these endpoints, on a shared app by default and a personal
one where that is set up ([how it connects](/how-it-connects/)):

- **Account:** `/me`, following, top artists and tracks, recently played
  (the last fifty plays; Fastpotify keeps its own longer
  [history](/using-fastpotify/#recent)).
- **Library:** playlists, saved tracks, albums, shows and episodes, and
  whether a set of items is saved. Saving and unsaving.
- **Playlists:** reading, creating, renaming, describing, making public or
  private, adding, removing and reordering songs, following and
  unfollowing. Uploading a cover image is possible and not built.
- **Catalogue:** albums, artists (with top tracks, discography, related
  artists), tracks, shows, episodes, search, and recommendations.
- **Playback control on any device:** the device list, transfer, play,
  pause, next, previous, seek, shuffle, repeat, volume, the queue, and
  adding to it.

Two limits come with it. Spotify's quota is shared by every user of the
default app, so heavy requests are paced and a `Retry-After` pauses that
session. And since November 2024 Spotify withholds Spotify-owned
playlists, related artists, recommendations, and audio features from newly
created apps, which is why a personal app cannot take every request and
complete playlist views stay on the shared one.

## What librespot's session gives

librespot signs in as the account and speaks the protocol Spotify's own
clients use. Fastpotify uses it for a few things the Web API cannot do:

- **The playlist tree with folders,** from the rootlist, in Spotify's order,
  read-only. Spotify's clients edit it through a diff protocol librespot
  does not implement, so folders cannot be made, renamed, or moved here.
- **Playlist permissions:** the rootlist is decorated with what the account
  may do to each playlist, which is how a playlist shared by invitation
  becomes editable (the Web API's `collaborative` flag does not cover
  those). Managing collaborators is a separate permission service, not
  implemented.
- **Lyrics** for the playing song, when Spotify has them.
- **Display names** behind the user ids the Web API returns for who added a
  song to a playlist.
- **Song radio** and autoplay, through Spotify's context resolver.

## What librespot's playback gives

- Streaming the catalogue to this computer at up to 320 kbps, gapless, with
  normalisation, a local audio cache, and this computer appearing as a
  **Spotify Connect device** that phones and other clients can transfer to.
- Shuffle, repeat, seek, and volume for that device.
- Podcast episodes as well as songs.
- **Spotify Premium is required.** Free accounts cannot stream through
  librespot at all.

Fastpotify runs a fork of librespot with a few small patches (queue
controls, normalisation-factor reporting for the visualisers, a distinct
event when Spotify refuses an audio key), listed in `Cargo.toml`. Anything
larger goes upstream first.

## What none of them reaches

Requests for the following cannot be built today. Each names the reason, so
an answer on the issue tracker can carry it.

- **Pinned playlists synchronised with the Spotify app.** Pins live in
  Spotify's client-internal library service (`your-library`), which has no
  Web API and which librespot neither implements nor has the protocol for;
  a probe of its endpoints over the session in September 2026 found nothing
  that answers. Fastpotify's pins are therefore its own, kept in
  `settings.json` on this machine. See issue #31.
- **Editing playlist folders.** Read-only, as above.
- **Smart Shuffle, Jam, Blend, and other features Spotify computes for its
  own clients.** They are not in the Web API and not in librespot. Shuffle
  here is plain shuffle.
- **Lossless audio.** librespot does not receive lossless streams; Fastpotify
  will reconsider it if librespot gains lawful upstream support, and will
  not bypass Spotify's DRM to get it. See `CONTRIBUTING.md`.
- **Local files.** librespot streams Spotify's catalogue only; a
  `spotify:local:` entry in a playlist has no audio it can fetch. Playing
  files from disk would be another player, not this one. See issue #3.
- **Audiobooks.** Not something librespot plays.
- **Offline listening, downloads, and any other way to keep the audio.**
  Ruled out by Spotify's DRM and by this project's boundaries.
- **Playback speed and crossfade.** librespot has neither. Speed for
  podcasts would have to be done in Fastpotify's own audio path, which is
  a feature request for the app rather than a Spotify limit; crossfade
  likewise.
- **Free-account playback,** and anything that substitutes audio from
  another service to get around it. Out of scope, see `CONTRIBUTING.md`.
- **Friend activity, private-session flags, and other client-internal
  social features.** No public surface.
- **Canvas videos and video podcasts.** Not carried by librespot.

## Answering a request

1. Find the feature above. If it is under one of the three surfaces and
   not built, it is a valid feature request; say what it would take.
2. If it is under the last section, answer with the reason quoted from
   here, link this page, and close the issue. Mention what the app offers
   instead when there is something (local pins, plain shuffle, the local
   play history).
3. If the reason has changed, because librespot or the Web API gained the
   capability, update this page in the same change that uses it.
