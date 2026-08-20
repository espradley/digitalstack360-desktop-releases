# DigitalStack360 Desktop — Changelog

All notable changes to the desktop app are documented here.
Versions follow [Semantic Versioning](https://semver.org).

---

## [1.6.6] — 2026-08-20

**Restores recovery controls for an enrolled Runtime Host when its readiness
cannot be read.**

### Fixed

- A locally enrolled Runtime Host whose account ownership is confirmed now
  retains **Disconnect** even when its server readiness check is unavailable.
  A failed account-host inventory remains non-actionable.
- **Reinstall Runtime** is now available for a confirmed enrolled computer.
  It deliberately replaces and restarts the managed daemon through the
  rollback-safe native installer, never accepts pairing material, and verifies
  that the same host identity remains in place before refreshing readiness.

---

## [1.6.5] — 2026-08-20

**Repairs an already enrolled Runtime Host after its local AI toolchain changes.**
An installed computer now remains identifiable as this computer when Claude
Code or Git needs attention, and can recheck the local toolchain without
pairing again.

### Fixed

- An enrolled Runtime Host with a missing Claude Code or Git prerequisite no
  longer falls back to **Set Up This Computer**. It presents its real lifecycle
  state and can refresh the discovered tool paths while retaining its host ID.
- Desktop release metadata is now aligned at `1.6.5` across the package,
  Tauri bundle, and native Cargo crate, so native diagnostics report the same
  version that the updater and release artifacts use.

---

## [1.6.4] — 2026-08-20

**Fixes "Disconnect this computer," which failed instantly in 1.6.3.** It
correctly disconnected the account from the server, then always reported a
generic error and left the local runtime behind. Fixed.

### Fixed

- "Disconnect this computer" always failed locally after successfully disconnecting on the server, reporting only "Unexpected error" and leaving the local runtime in place. The underlying step's status messages were landing in the same output the app reads as a single structured result, so that result never parsed. It's a clean structured result now.

---

## [1.6.3] — 2026-08-20

**"Set Up This Computer" now recognizes an already-set-up machine, and you
can disconnect one.** Previously the checkbox and button always appeared,
even on a computer that was already fully working — clicking through the
whole setup flow again was the only way to find that out. Now a working
computer just says so, with a Disconnect action in its place.

### Added

- The Connected Providers page now checks, before showing any button, whether this computer is already set up and ready. If it is, that's what you see — not a checkbox and button that would just repeat work already done.
- A "Disconnect this computer" action, available whenever this computer is enrolled. Disconnecting removes it from the account and clears its local runtime, and the normal setup checkbox/button return immediately afterward.

---

## [1.6.2] — 2026-08-20

**Fixes an app freeze that could happen right after signing in or updating.**
Under a specific timing race, the whole app window could stop responding to
any click — nothing was actually crashed, but nothing worked either, and the
only way out was to force-quit. Fixed.

### Fixed

- The app could freeze completely, with every click unresponsive, if the menu-bar tray icon's once-a-second status update happened to overlap with the app checking your sign-in state — most likely right after sign-in or right after an app update relaunches. The two were contending for the same internal lock in a way that could deadlock them against each other. They no longer contend for it.
- Launching the app used to leave a blank, unresponsive-looking window for up to 15–25 seconds while it loaded over the network, with no indication anything was happening. It now shows a small branded loading window immediately, which is replaced by the real app the moment it's actually ready.

---

## [1.6.1] — 2026-08-20

**Fixes Set Up This Computer, which crashed instantly in 1.6.0.** The bundled
runtime installer failed to start at all in the notarized 1.6.0 build,
so Set Up This Computer could never get past its first step. 1.6.1 fixes it.

### Fixed

- The bundled runtime crashed immediately on launch under macOS's Hardened Runtime, which this app has been signed and notarized with since 1.6.0. It needed two additional entitlements to let its JavaScript engine start; it now has them.

### Correction

- 1.5.0 and 1.6.0 both said builds were unsigned. That was already out of date: builds have been signed with a real Apple Developer ID and notarized since before 1.6.0 shipped.

---

## [1.6.0] — 2026-08-20

**"Set Up This Computer" is now a real, one-click action.** 1.5.0 shipped the
bundled runtime and pairing; this release completes the chain it didn't yet
have — the Desktop can now actually install and start the local runtime for
you, not just enroll this machine. macOS only, unchanged from 1.5.0.

### Added

- **Set Up This Computer** — one action, on the Connected Providers page: pairs this machine if needed, installs the packaged runtime, registers it with launchd, starts it, and waits for it to report healthy. Replaces an existing legacy runtime service safely if one is registered, with no manual `launchctl`. If anything fails partway through, the previous working runtime (if any) is automatically restored rather than left half-migrated.
- **Native capability check** — before attempting setup, the hosted page asks this Desktop build what it can actually do. An older installed Desktop that doesn't support this yet is told plainly to update, instead of failing in a confusing way.
- **Execution readiness after setup** — once the runtime is installed and healthy, the page checks whether this computer is actually ready to run AI work (e.g. Claude Code signed in, git available) and shows a clear status: ready, or exactly what still needs attention.

### Fixed

- The desktop release pipeline's Intel build was requesting a macOS runner image GitHub had retired, which silently stalled every release before it could publish. Fixed.

### Notes

- These native capabilities are macOS-only by design. On Windows/Linux the runtime-host commands are not present and this action does not appear.
- Builds remain unsigned in this release (right-click → Open on first launch); signing/notarization is tracked separately.

---

## [1.5.0] — 2026-08-19

First release carrying the **Program DI Daemon Installer** — the native
capabilities that let this computer run AI executions locally. macOS only (the
runtime is a launchd daemon); Windows and Linux builds ship the existing timer,
activity, and viewer features unchanged.

### Added

- **Set up this computer / pairing** — the Desktop can enroll this machine with your workspace: it redeems a pairing code minted by the hosted app and installs the DigitalStack runtime as a managed background service (DI-1C sidecar, DI-2 pairing).
- **Runtime status light** — the title bar shows whether this computer is ready to run AI work, driven by the composed server-side execution-readiness contract (DI-7): steady green when ready and idle, a gentle pulse while executions are active, amber when setup is needed, grey when readiness cannot be established. Click it to open the execution queue.
- **Bundled runtime-host sidecar** — the runtime ships inside the app bundle and is invoked directly (never from `PATH`), so the Desktop always talks to the runtime it shipped with.

### Notes

- These native capabilities are macOS-only by design. On Windows/Linux the runtime-host commands are not present and the readiness light does not appear.
- Builds remain unsigned in this release (right-click → Open on first launch); signing/notarization is tracked separately.

---

## [1.3.5] — 2026-05-12

### Fixes

- **Release build compile error** — The `download_and_install` Rust closure signatures were wrong in the release-only code path (unreachable in dev builds, so it passed local compilation). First closure now correctly takes `(chunk, total)`, second returns `()` not `Result`. 1.3.3 and 1.3.4 both failed CI for this reason.

---

## [1.3.4] — 2026-05-12

### Fixes

- **Auto-update banner** — The background update check (`bootUpdateService`) uses the plugin's combined `downloadAndInstall` command, which requires `updater:allow-download-and-install`. 1.3.2 only granted `allow-download` and `allow-install` separately, so the download silently failed and the in-app banner never appeared. Permission now correctly granted.

---

## [1.3.3] — 2026-05-12

### Fixes

- **Check for Updates — native dialog** — "Check for Updates…" now shows a native macOS dialog with **Install & Restart** and **Later** buttons instead of relying on the timer dock being open to surface the action. The entire flow runs in Rust: the active timer is saved, the update is downloaded, installed, and the app restarts without requiring any webview window.

---

## [1.3.2] — 2026-05-11

### Fixes

- **Auto-update download** — The `download_and_prepare_update` command was blocked by Tauri's ACL when called from the hosted main window (a remote-origin context). Added `updater:allow-download`, `updater:allow-install`, and an explicit app-command permission so the update flow completes correctly without an ACL error.

---

## [1.3.1] — 2026-05-10

### Fixes

- **Window → Open Timer crash** — Two new plugins introduced in 1.3.0 (`tauri-plugin-window-state` and `window-vibrancy`) each caused the process to terminate when the timer dock was opened for the first time. `window-vibrancy` panics before the `WKWebView` finishes initialising; `tauri-plugin-window-state` panics when it encounters an `always_on_top` window with no prior saved state. Both have been removed pending a safe re-implementation.
- **Delivery role seeding** — Eager-seed `delivery_roles` before role resolution to prevent missing-role errors on first project load.

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
