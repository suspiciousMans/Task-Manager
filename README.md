# Task Manager

A simple task manager with a list view and a month calendar view. Tasks
support a title, notes, due date/time, priority, and category, and persist
to `localStorage`. No build step, no framework, no backend.

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

## Project layout

- `app/` — the task manager itself (`index.html`, `styles.css`, `app.js`).
  Framework-free, so this folder can be dropped into any static site as-is.
- `main.js`, `preload.js` — Electron main process, wraps `app/` in a
  desktop window.
