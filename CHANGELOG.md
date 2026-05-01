# DigitalStack360 Desktop — Changelog

All notable changes to the desktop app are documented here.
Versions follow [Semantic Versioning](https://semver.org).

---

## [1.2.2] — 2026-05-01

### Fixes

- **Time Dock — categories and contexts stuck on "Loading…"** — Production release builds were calling the apex `digitalstack360.com`, which Vercel 308-redirects to `www.`.  `fetch` strips the `Authorization` header on cross-origin redirects, so every `/api/desktop/*` request arrived at `www.` unauthenticated and 401'd.  Repointed release-build defaults at `https://www.digitalstack360.com` directly.  As an interim workaround, anyone on 1.2.1 can set `DIGITALSTACK_URL=https://www.digitalstack360.com` in their environment.
- **Silent dock failures** — when category / project / work-target loads failed, the API client silently returned an empty list and the dock UI rendered "Loading…" indefinitely.  Errors are now surfaced inline in the Context and Category sections with a Retry button so failures are diagnosable without devtools.

---

## [1.2.1] — 2026-04-29

### Fixes

- **Auto-update signature verification** — 1.2.0's release artifacts were signed with a CI key that didn't match the app's embedded updater public key, so the in-app "Update available" banner failed at the verification step.  1.2.1 ships with the correct signing key restored in CI.  Users on 1.1.2 and 1.2.0 will auto-update to 1.2.1 normally.  Anyone on 1.2.0 who got stuck can also grab 1.2.1 manually from the download page.

  No code or feature changes from 1.2.0 — same release, correctly signed.

---

## [1.2.0] — 2026-04-29

### What's new

- **Meeting presence detection** — DigitalStack now recognises when you're in a Google Meet, Zoom, Microsoft Teams, Webex, or Slack huddle.  Detection survives task-switching with a 120-second sticky window, catches Meet picture-in-picture popups and background tabs, and surfaces the meeting topic alongside the matched project.  Sessions now record meeting context so AI summarisation can reason about overlap.
- **Activity Rules drive detection** — your Time → Rules entries now flow into the desktop's matcher.  Title-token rules catch window titles and document content; **domain rules** (new) catch browser surfaces by hostname; **app rules** (new) catch native tools like Cursor or VS Code.  The customer's `clientName` is auto-added as a default alias per project.
- **Possible-mismatch banner with one-click reassign** — when a session was tracked to one project but evidence pointed to another, the session card flags it.  A single click moves the session to the detected project; an audit row records the change.  Detected fields stay untouched as evidence.
- **Browser Automation in Privacy & Permissions** — a third permission card explains and unlocks live URL detection in Chrome / Safari.  Required for project / ticket / meeting detection on browser-based work; auto-probed in the background once granted.
- **Ticket detection from in-app navigation** — clicking through DigitalStack tickets in the desktop app now surfaces the ticket key without requiring browser-style URLs.  The accessibility tree's web-area URL fills in.
- **Selected vs Detected separation** — the timer's chosen project (authoritative) is now distinct from what we infer from window titles / URLs (evidence).  When they disagree, that's a signal the AI can reason about, not a bug.

### Under the hood

- New Privacy & Permissions card surfaces Browser Automation explicitly so the macOS prompt doesn't appear unannounced.
- Automation grants persist across app launches (TCC state was always there; we now read it).
- Project rules sync from the main webview every 60 seconds so detection works even when the timer dock is closed.
- Backfill action (dev-only) replays local activity events through the matchers to populate older sessions.

---

## [1.1.2] — 2026-04-26

### What's new

- **Time Dock** — A tray-anchored floating window gives you a live timer, one-click project switching, and instant entry submission without opening the main app. Choose *Compact* (timer + single project) or *Full* (all projects) mode from the dock itself. Favorites, quick-apply, and entry history are coming in the next update.
- **Execute module** — The desktop app now streams project execution updates in real time. Start, pause, and monitor long-running tasks directly from the tray window.

### Fixes

- Idle timer color contrast improved — the elapsed-time display is now clearly readable against the dark dock background.

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
