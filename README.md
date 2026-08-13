# Task Manager

A simple task manager with a list view and a month calendar view. Tasks
support a title, notes, due date/time, priority, and tags, and persist to
`localStorage`. No build step, no framework, no backend — except for the
optional cross-device sync below, which talks directly to Firebase from
the browser.

## Run in a browser

Open `app/index.html` directly, or serve the `app/` folder with any static
file server:

```sh
npx serve app
```

## Run as a desktop app (Electron)

```sh
npm install
npm start
```

## Build a desktop installer

```sh
npm install
npm run dist
```

Outputs to `release/` (AppImage on Linux, dmg on macOS, nsis installer on
Windows — see the `build` block in `package.json`).

## Cross-device sync (one-time setup)

The "Sync across devices" panel pushes/pulls tasks through the same
Firebase Realtime Database project the site's chat page already uses
(`chat-d4d38`), under a separate `taskSync/<code>` path. The client config
in `app.js` is not secret (Firebase configs never are); access is
controlled entirely by the database's security rules, which don't yet
allow this new path. Until you add the rule below, connecting will show
a "permission denied" error.

**To enable it:** open the [Firebase console](https://console.firebase.google.com/)
→ your `chat-d4d38` project → Realtime Database → **Rules**, and add a
`taskSync` block as a sibling of the existing `messages` rules, e.g.:

```json
{
  "rules": {
    "messages": {
      // ...your existing chat rules, unchanged...
    },
    "taskSync": {
      "$code": {
        ".read": true,
        ".write": true,
        ".validate": "newData.hasChildren(['tasks', 'updatedAt', 'updatedBy'])"
      }
    }
  }
}
```

Then **Publish**. No app changes needed after that — just hit Connect
again.

**How it works:** click **Generate** for a random code (or make up your
own), then enter that same code on your other device and connect. Each
device pushes its full task list on every change and listens live for
updates from the other; the most recent write wins if both edit at once.

**Security note:** the code *is* the access control — like a shared,
unlisted link. Anyone who has it (or guesses it) can read and overwrite
that code's tasks, and there's no way to revoke access short of switching
to a new code. Use the generated random code rather than a short/guessable
one, and don't share it anywhere public.

## Project layout

- `app/` — the task manager itself (`index.html`, `styles.css`, `app.js`).
  Framework-free, so this folder can be dropped into any static site as-is.
- `main.js`, `preload.js` — Electron main process, wraps `app/` in a
  desktop window.
