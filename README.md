<h1 align="center">WhisperOrigo Releases</h1>

<p align="center">
  <img src="assets/whisperorigo-icon.png" width="128" alt="WhisperOrigo logo">
</p>

<p align="center">
  Signed and notarized release artifacts for WhisperOrigo.
</p>

<p align="center">
  <a href="../../releases/latest"><img src="https://img.shields.io/github/v/release/timtey88/WhisperOrigo-Releases?style=flat" alt="Latest Release"></a>&nbsp;
  <img src="https://img.shields.io/badge/macOS-26.0+-000?style=flat&logo=apple" alt="macOS 26.0+">
</p>

## Installation

1. Download the latest `WhisperOrigo-*.zip` from [Releases](../../releases/latest).
2. Unzip it and move `WhisperOrigo.app` to `/Applications`.
3. Launch WhisperOrigo and grant the requested macOS permissions.

WhisperOrigo is signed with Developer ID and notarized by Apple. Sparkle
verifies future updates with the app's Ed25519 public key.

## Updates

WhisperOrigo checks this repository's appcast at:

`https://timtey88.github.io/WhisperOrigo-Releases/appcast.xml`

Automatic checks are currently disabled while the app is in beta. Use the
manual **Check for Updates...** command in the app when update support is
available.

## Repository contents

- `appcast.xml` is published through GitHub Pages for Sparkle.
- GitHub Releases host the downloadable ZIP artifacts.
- The source code remains in the private WhisperOrigo repository.
