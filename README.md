# DigitalStack360 desktop releases

Auto-update artifacts for the DigitalStack360 desktop app.

The source repository (`espradley/digitalstack360`) is private. This public
repo exists so the in-app updater can fetch signed artifacts and the
`latest.json` feed over plain HTTPS without authentication.

Releases here are tagged `desktop-vX.Y.Z` and published by the
`Release Desktop` GitHub Actions workflow in the source repo. Each
release contains:

- macOS DMG installers (Apple Silicon + Intel)
- Windows NSIS `-setup.exe` installer
- Tauri updater artifacts (`.app.tar.gz` for macOS, `.nsis.zip` for
  Windows) + matching `.sig` signatures
- `latest.json` feed consumed by the desktop app on auto-check
