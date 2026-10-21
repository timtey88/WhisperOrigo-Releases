<h1 align="center">WhisperOrigo Releases</h1>

<p align="center">
  <img src="assets/whisperorigo-icon.png" width="128" alt="WhisperOrigo logo">
</p>

<p align="center">
  Release downloads and update feed for WhisperOrigo, a private local dictation app for macOS.
</p>

<p align="center">
  <a href="https://github.com/timtey88/WhisperOrigo-Releases/releases/latest"><img src="https://img.shields.io/github/v/release/timtey88/WhisperOrigo-Releases?style=flat" alt="Latest Release"></a>&nbsp;
  <img src="https://img.shields.io/badge/macOS-26.0+-000?style=flat&logo=apple" alt="macOS 26.0+">
</p>

**Contents:** [Features](#features) · [Installation](#installation) ·
[Privacy](#privacy) · [Updates](#updates) · [Repository contents](#repository-contents)

## Features

- Dictate with a configurable shortcut in toggle or hold mode.
- Choose Apple Speech or download Parakeet V2/V3 for local transcription with live previews.
- Compare model languages, reference ratings, sizes, and capabilities in a searchable workspace.
- Keep transcripts in the app, copy them, or paste into your active app.
- Apply optional on-device cleanup, filler removal, and vocabulary replacements.
- Customize audio input, recording sounds, and the transcript overlay.
- Opt into local history with playback, favorites, model-specific retries, transcript versions, and retention controls.

## Installation

Requires an Apple silicon Mac running macOS 26.0 or later.

1. Download the latest `WhisperOrigo-*.zip` from [Releases](https://github.com/timtey88/WhisperOrigo-Releases/releases/latest).
2. Unzip it and move `WhisperOrigo.app` to `/Applications`.
3. Launch WhisperOrigo and complete setup. Allow microphone access for recording
   Input Monitoring access for the global shortcut, and Accessibility access for automatic paste.
4. Focus a text field, press **Right Option**, speak, then press it again to stop.

The default output is **Keep transcript here**. Open **Settings → Dictation** to
choose automatic paste or clipboard copy.

Published release artifacts must be signed with Developer ID and notarized by Apple.

## Privacy

Speech and optional cleanup run on this Mac, without accounts, cloud transcription,
analytics, or telemetry. History is off by default. Enabling it saves recordings
and transcripts locally; turning it off keeps existing entries.

Speech assets and Parakeet models may need an initial download. Offline dictation
requires the selected model/language assets to be installed. Optional cleanup depends on Apple
Intelligence readiness and language support; when unavailable, the app keeps the
raw transcript.

## Updates

Automatic checks are off by default. Use **Settings → About → Check for updates…**
to check manually. Speech-asset downloads and app update checks/downloads require
network access.

Sparkle verifies updates with the app's Ed25519 public key. The update feed is
[appcast.xml](https://timtey88.github.io/WhisperOrigo-Releases/appcast.xml), published
through GitHub Pages.

## Repository contents

- [GitHub Releases](https://github.com/timtey88/WhisperOrigo-Releases/releases) host
  downloadable ZIP artifacts, SHA-256 checksums, and release notes.
- [appcast.xml](appcast.xml) contains the Sparkle update feed.
- [1.1.0 notes](<release-notes/1.1.0.md>) describe Parakeet support and the comparison workspace.
- [1.1.0 preparation record](<records/1.1.0.md>) tracks candidate evidence and qualification status.
- [1.0.0 notes](<release-notes/1.0.0.md>) and its [preparation record](<records/1.0.0.md>) preserve the first-release history.
- `assets/` contains public app branding.
- The source code remains in the private WhisperOrigo repository.

Report problems through [Issues](https://github.com/timtey88/WhisperOrigo-Releases/issues).
