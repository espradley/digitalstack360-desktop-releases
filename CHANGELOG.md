# DigitalStack360 Desktop — Changelog

All notable changes to the desktop app are documented here.
Versions follow [Semantic Versioning](https://semver.org).

---

## [1.1.0] — 2026-04-25

### What's new

- **Time Hub** — Work now has a single unified **Time** section replacing the separate My Time and Activity tabs. Inside Time: an **Entries** tab for submitted records, a **Timer** tab for fast manual entry, an **Activity Review** tab for the full Activity Assist experience, and a **Rules** tab for managing domain/app/title routing rules.
- **Tray menu updated** — Right-click now shows *Open Time*, *Review Activity*, and *Sync Activity* at the top. Left-click opens the Time Hub panel directly.
- **Activity auto-assignment** — Activity Assist now automatically assigns time blocks to the correct project when DigitalStack is the active window, with 100% confidence. No manual tagging needed for work done inside the app.
- **Smarter activity clustering** — Meeting apps (Zoom, Teams, etc.) always create a task boundary. Sustained switches to a different app (3+ minutes) split untagged blocks, while brief context switches under a minute are absorbed. This produces cleaner, more accurate suggested tasks.
- **OS window titles** — The desktop app now tracks which project and section you are in. This powers the auto-assignment above and improves activity summaries.

### Fixes

- Activity project assignments now persist correctly across re-analysis. Re-running inference no longer clears previously assigned projects.

---

## [1.0.3] — 2026-04-25

### Fixes
- Windows and Linux downloads now appear on the download page. Tauri v2 signs both the Windows installer (`.exe`) and Linux AppImage directly rather than wrapping them in a tarball. The build pipeline has been updated to stage and publish the correct artifacts and wire them into the auto-update feed.

---

## [1.0.2] — 2026-04-25

### Fixes
- Windows auto-update now works correctly. Tauri v2 signs the NSIS installer directly (`.exe.sig`) rather than producing a zip bundle. The previous release pipeline was looking for the wrong file pattern and skipping Windows in the update feed.
- Linux auto-update artifact (`AppImage.tar.gz`) is now produced correctly. The `appimage` bundle target was missing from the build config, which prevented the signed updater bundle from being generated.

---

## [1.0.1] — 2026-04-25

### What's new
- **About dialog** — DigitalStack360 menu → About shows the current build version and a link to the website.
- **Edit menu** — Cut, Copy, Paste, Undo, Redo, and Select All are now wired through the native macOS menu. This fixes keyboard shortcuts (`Cmd+C`, `Cmd+V`, etc.) in text fields throughout the app.
- **Show DigitalStack shortcut** — `Cmd+Shift+M` brings the main window forward from anywhere, or reopens it if you had closed it.

### Fixes
- Auto-update now ships signed artifacts for all four platforms (macOS Apple Silicon, macOS Intel, Windows, Linux). Linux was missing the updater bundle in 1.0.0 due to a build config oversight.
- The CI pipeline now builds and publishes each platform independently — a slow or unavailable runner on one platform no longer delays the others from shipping.

---

## [1.0.0] — 2026-04-25

### Initial beta release

- **Always-on timer** — Start, pause, stop, and resume time entries from a tray-anchored window. Entries sync to your DigitalStack workspace automatically.
- **Activity Assist** — Opt-in passive capture (app names and window titles, never screenshots or keystrokes). The engine clusters your day into time blocks, scores them against your projects, and drafts entry descriptions you can edit and submit.
- **Activity controls** — Set your retention window (7, 30, 90 days, or keep forever). Delete any day's data or wipe everything at any time.
- **.dstack document viewer** — Open `.dstack` engagement bundles natively. Read manifests, content, and bindings without uploading the file anywhere.
- **Native notifications** — Get notified about review requests, approvals, and stream activity. Click to jump straight to the right page.
- **Auto-update** — New versions download in the background. A non-blocking banner lets you restart when it suits you. Active timers are saved cleanly across the relaunch.
- **macOS menu bar** — The status bar item shows elapsed time when a timer is running and gives you one-click access to Start, Pause, Stop, and the main app.
- **Tray stays alive** — Closing the main window does not quit the app. A running timer keeps going until you explicitly stop it or quit from the tray.

### Platforms
| Platform | Format | Notes |
|---|---|---|
| macOS Apple Silicon | `.dmg` | Unsigned beta — right-click → Open to bypass Gatekeeper (one-time) |
| macOS Intel | `.dmg` | Same as above |
| Windows x64 | `.exe` installer | Unsigned beta — click "More info" → "Run anyway" in SmartScreen (one-time) |
| Linux x64 | `.AppImage` | Requires `libfuse2`. `chmod +x` then run. |
