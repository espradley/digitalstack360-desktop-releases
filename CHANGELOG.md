# DigitalStack360 Desktop — Changelog

All notable changes to the desktop app are documented here.
Versions follow [Semantic Versioning](https://semver.org).

---

## [1.3.0] — 2026-05-09

### What's new

- **Find in page (Cmd+F)** — Full-text search across any page in the desktop app. The find bar highlights all matches in yellow, steps through them with Enter / Shift+Enter or the arrow buttons, and closes on Escape. Edit → Find Next (Cmd+G) and Find Previous (Cmd+Shift+G) work from the menu bar too.
- **Launch at Login** — Privacy & Permissions now has a Launch at Login toggle so Activity Assist and the timer dock are ready from the first moment of your day.
- **System-wide timer shortcuts** — Control the timer without switching to the app: **Cmd+Shift+;** starts, pauses, or resumes the active timer; **Cmd+Shift+'** opens or focuses the timer dock.
- **Timer dock near tray** — The timer dock now opens anchored to the system tray icon so it always appears where you expect it, and has a frosted-glass background on macOS.
- **Window position persists** — The main window remembers its size and position across restarts.
- **Single-instance enforcement** — Clicking the Dock icon or launching from Spotlight while the app is already running focuses the existing window instead of opening a duplicate.
- **`digitalstack360://` deep links** — The app registers the `digitalstack360://` URL scheme. Clicking a deep link from any app or email focuses DigitalStack360 and navigates to the target page.

### Fixes

- **Safari colour rendering** — All colour tokens now have hex fallbacks for Safari < 15.4, which doesn't support the `oklch()` colour space. Older macOS and iOS devices no longer render the app without colour.
- **Safari form controls** — `<select>` dropdowns and date/time inputs no longer show Safari's heavy native chrome. All form controls are now fully styleable and consistent across browsers.
- **Scope template phase references** — The `qa` delivery phase rename to `uat` (introduced in 1.2.11) was not applied to the built-in scope templates, causing a TypeScript build failure. All three templates (New Build, Replatform, Enhancement) now use `uat` correctly.

---

## [1.2.11] — 2026-05-08

### What's new

- **Quick-access favorites bar** — The macOS title bar now has a persistent favorites strip. Hit **+** to bookmark any page; hover for a full title tooltip; right-click to rename or remove. Favorites survive app restarts.
- **Context-aware title bar** — The title bar now shows the active module, project, or board rather than always displaying "DigitalStack360".

### Fixes

- **Windows & Linux builds** — The overlay title bar introduced in 1.2.10 used a macOS-only Tauri API that prevented the app from compiling on Windows and Linux. Title bar customisation is now gated to macOS; Windows and Linux continue to use the platform's native frame.

---

## [1.2.10] — 2026-05-06

### What's new

- **Delivery role rate card** — Bill and cost rates configured in Workspace → Delivery Roles now flow through to all estimate resource planning tabs automatically. Rates no longer default to a generic $175/hr placeholder.
- **Release architecture in estimates** — Engagement estimates now support release-based scoping: work packages can be assigned to a named release, the Timeline shows release bands, and the Resources tab rolls up costs per release.
- **Engagement structure** — Workstreams can be grouped under Domains (business areas) for multi-domain engagements. Scope, Effort, and Resource Planning views all respect the Domain grouping.

---

## [1.2.9] — 2026-05-04

### What's new

- **Keyboard shortcuts** — Press **?** anywhere in the app to open the shortcut reference. Board navigation, modal dismiss, and panel open/close all have keyboard bindings.
- **Native Save-As for exports** — CSV and file exports now use the OS Save dialog instead of a forced browser download, so you can choose the destination folder directly.
- **Capture Assist AI summaries** — Work sessions captured by Activity Assist are now automatically summarised by AI. Summaries appear on the Session detail view and feed into the Day Story.
- **Improved update banner** — The "update available" banner in the timer window now stacks vertically so it no longer clips in narrow mode.

---

## [1.2.8] — 2026-05-03

### What's new

- **Capture Assist — dual-window aggregation** — Activity Assist now uses a short window for transition detection and a long window for stability, reducing false-positive session splits on brief context switches.
- **Meeting title detection** — Calendar event titles now appear on meeting span bars. The classifier is title-aware and auto-marks attendance; meeting spans from fragmented activity blocks are merged correctly.
- **Day Story** — A new AI-enhanced close-of-day summary synthesises your activity blocks, captured sessions, and meeting attendance into a narrative you can review and submit.

### Fixes

- Billable target calculation now uses your weekly quota instead of tracked ratio.
- Activity window detection skips indicator-overlay windows when identifying the active candidate.

---

## [1.2.7] — 2026-05-02

### What's new

- **Work Dashboard** — A redesigned week command-centre shows planned hours, allocation percentages, billable target vs. actual, and a "Where time went" project breakdown in the right panel.
- **Billing — promote approved sessions** — Approved time submissions can now be promoted directly into billable line items without leaving the app.
- **Help centre** — A searchable help centre is available at DigitalStack360.com/app/help and linked from the desktop app's menu.

---

## [1.2.4] — 2026-05-01

### Fixes

- **Activity window candidate selection** — The active window detection now skips transparent indicator overlays (e.g. screen-recording status lights) when choosing the candidate window for project matching, eliminating spurious no-match results on macOS Sonoma.

---

## [1.2.3] — 2026-05-01

### Fixes

- **Auto-update signature verification on macOS Apple Silicon** — 1.2.2's release pipeline produced two artifacts named `DigitalStack360.app.tar.gz` (one per architecture) and uploaded them to the same GitHub release with `--clobber`.  Whichever Mac runner finished second silently overwrote the other, so the URL served the wrong-architecture binary while the manifest signature was still pinned to the original.  Apple Silicon users got an Intel binary that failed verification.  The release workflow now namespaces the macOS bundle filenames per architecture (`DigitalStack360_aarch64.app.tar.gz` and `DigitalStack360_x86_64.app.tar.gz`) so the two never collide.  No code changes from 1.2.2 — same fixes, signed correctly per architecture.

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
