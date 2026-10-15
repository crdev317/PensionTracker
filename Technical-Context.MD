# Technical Context

Engineering-practice contract for code-writing agents in this project. Every implementation must respect what's written here. If a constraint conflicts with the user's request, flag the conflict — don't silently bypass it.

## Operating Context

Every later section of this contract is written for the Operating Context stated here.

- **Users:** One person, the owner, tracking their own pensions.
- **Operating Scale:** A single user and negligible traffic. One person builds it with Factory agents, part-time.
- **Where it runs:** A Windows desktop app on the owner's own machine, used in their interactive session. Factory agents build and test it headless in Linux cloud containers.
- **Credentials:** None. The app has no login of its own and holds no credentials for outside services; the owner's Windows sign-in is the only access control.
- **Alongside it:** None.
- **Not the Product's concern:** The Windows machine itself (updates, antivirus, disk encryption). Backups of the machine and its files, including the app's data. The pension providers' systems and statements: the app stores what the owner enters from them and never connects to or reconciles against a provider.
- **Last confirmed:** 2026-10-15

## Overriding Principles
1. **Pension data never leaves the machine.** No network calls, telemetry, analytics or cloud sync — the owner's financial data is personal and stays local.
2. **No real pension data in the repo.** Tests, fixtures and examples use made-up figures only — the repo is on GitHub and agents read it.
3. **Never lose or corrupt saved data.** A change to the stored format ships with a migration that reads the old format — the owner's history is the whole point of the app.
4. **Keep it simple.** No feature, abstraction or dependency until a Story needs it; the standard library comes first — it is a one-person app, and every addition is upkeep.
5. **No architectural change without an ADR.** Decisions stay traceable for future agents.

## Frameworks
- **Python 3.13** — the language and runtime; standard library only.
- **Tkinter (Tk 8.6, bundled with Python 3.13)** — the desktop UI.
- **One JSON file, via the standard-library `json` module** — the only persistent store, at `%APPDATA%\PensionTracker\pensions.json`, carrying a format version number that migrations key on.

## 3rd-party tech
- **GitHub** — code hosting and pull requests (`crdev317/PensionTracker`).
- **Azure DevOps Boards** — the tracker (`EnateInternal` / `Pension Tracker`); an Azure DevOps pipeline reports the `ci` check on PRs once it is wired.
- **Claude Code / Factory agents** — build the app; nothing calls them at runtime.
- **At runtime:** nothing. The app calls no outside service.

## Packages in use
**UI:** `tkinter`, `tkinter.ttk`, `tkinter.messagebox`
**Storage:** `json`, `pathlib`
**Money:** `decimal.Decimal` for every amount — never `float`
**Dates:** `datetime`
**Logging:** `logging`, `logging.handlers.RotatingFileHandler`
**Testing:** `unittest`, `tempfile`
**Third-party:** none. A new dependency needs a Story that requires it and an ADR.

## Toolchain pinning
- Policy: range — the minor version is pinned, so any Python 3.13.x patch release is accepted and 3.14 is not.
- Pinned tools: Python 3.13, pinned by the one-line `.python-version` file (`3.13`) at the repo root, which agents, CI and the owner all read. There are no dependencies, so there is no lockfile.
- Bumping: a PR that changes `.python-version`, merged only when the Tier 1 suite passes on the new version on Linux and Windows.

## Instrumentation
- Python `logging`, plain text, to `%APPDATA%\PensionTracker\logs\app.log`, rotating at 1 MB and keeping 3 old files.
- Levels: `INFO` by default; `ERROR` means something failed and the user saw an error; `DEBUG` is switched on by hand only when investigating.
- Always logged: app start and stop with the app version; every load and save of the data file, success or failure; every format migration; every unhandled exception with its traceback.
- Never logged: pension amounts, provider names, policy numbers or any other value the owner entered. The log records *that* something happened, not the owner's data.
- No metrics, tracing or telemetry (Overriding Principle 1).

## User Feedback Approach
- Channels: in the app only — no emails or notifications. A short status-bar message confirms a successful save; a dialog is used only for errors and for confirming anything destructive, such as deleting a pension.
- Tone: plain, short, friendly British English. An error message says what happened and what to do next ("Couldn't save your changes. Check the disk isn't full, then try again."). No technical jargon or tracebacks — those go to the log.
- Formats: money as `£12,345.67`; dates as `15/10/2026` (DD/MM/YYYY). Pensions are UK pensions.

## Branching and Environments
- Model: trunk-based.
- Branches: `main`; `feature/<id>-<slug>` per Feature; `story/<id>-<slug>` or `bug/<id>-<slug>` per item, merged into its Feature's branch, or into `main` when it has no parent Feature. Full rules in `CONTRIBUTING.md`.
- Environments: Dev/test is the Factory agents' Linux containers, plus the `ci` pipeline once it is wired. Production is the owner's Windows PC, running from a local clone of `main` through a desktop shortcut that runs `pythonw`; a `git pull` takes a new version. There is no installer and no packaged `.exe`.
- Protected: `main` — PR only, squash merge only, linear history, no force-push, no deletion. `ci` becomes a required check once it is wired.

## Git Guardrails
- Never without explicit confirmation from the owner: `git push --force` (or `--force-with-lease`), `git reset --hard`, `git clean -f`, rewriting history on a shared branch, `git commit --no-verify`.
- Never read, write, move or delete anything under `%APPDATA%\PensionTracker\` — it holds the owner's real data and logs. Tests always use a temporary folder.
- Hooks in place: `.githooks/pre-commit` blocks scratch and diff dump files from a commit. Each clone enables it once with `git config core.hooksPath .githooks`.
- **HITL delivery opens and closes its own board state — nothing else will.** No skill in the HITL pipeline (`/factory-to-stories`, `/factory-check-security-design`, `/factory-triage`) sets an assignee, an iteration, or an in-progress state at creation — they deliberately leave a HITL-tagged Story, Bug, or wholly-HITL Feature untouched for a human to clear. So: **before** making any change while hand-delivering one, set `System.AssignedTo` to yourself, `System.IterationPath` to the current sprint, and `System.State` to `Active`. For a Feature, delivery starts with its first Story; while it is planned, the planning skills set its state. **When** the delivering PR merges and the work item is done, set `System.State` to `Resolved`/`Closed` yourself before ending the session. Skipping either half leaves the item unassigned, out of sprint, and looking untouched for the entire time it's actually being worked.

## Testing & the ratchet
> Seeded from the Factory standard — **opt-out, not opt-in**. Trim a line only with a deliberate, recorded decision; don't start from nothing. Full standard: <https://github.com/enateltd/factory-skills/blob/main/docs/testing-and-the-ratchet-standard.md> · the *why*: <https://github.com/enateltd/factory-skills/blob/main/docs/agentic-e2e-testing-principles.md>.

- **Every seam gets a real-IO test on at least one side.** Mock-on-both-sides only proves the mocks agree; defects live at integration seams, not inside modules. The Product's one seam is its JSON data file, so tests read and write real files in a temporary folder.
- **Two tiers, each catching a class the other can't.** Tier 1 — `unittest` with real local file I/O in a fresh temporary folder per run; $0, every commit. Tier 3 — live: the owner's own day-to-day use on Windows.
- **Platform matrix is non-negotiable for OS-touching code.** The app touches the filesystem, so Tier 1 runs on Linux (the agents' containers) and on Windows (the `ci` pipeline, once it is wired).
- **Plan test coverage at the Feature level, not the Story level.** Each Feature declares at kickoff which Tier 1 tests and fixtures it needs.
- **The ratchet — every Tier-3 defect pins a Tier-1 regression.** A defect the owner hits in real use is fixed together with a `unittest` that would have caught it, encoding the **class** of defect **bounded by the seam** where it surfaced — not the exact reproducer, and not a project-wide sweep. A Story that surfaced from a live defect carries that ratcheting test as an acceptance criterion.
- **Assert on observable behaviour, not internal state.** Tests assert on what the saved file holds and what the user sees, not on internals.
- **Trimmed by deliberate decision (2026-10-15):**
  - *Test the contract, not the LLM* — the Product has no LLM.
  - *Tier 2 (live-LLM, disposable)* and *Disposable substrate, not shared* — the Product makes no LLM calls and talks to no outside system; Tier 1's fresh temporary folder per run already isolates every test.
