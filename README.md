# URL Shortener — Frontend

A Vue 3 + Vite single-page app for [kurang.click](https://api.kurang.click), a URL shortener that supports custom short codes and expiry dates.

## Prerequisites

- Node.js 18+
- npm 9+

## Getting started

```bash
# Install dependencies
npm install

# Start the dev server (http://localhost:5173)
npm run dev
```

The app talks to `http://localhost:8080` by default. Make sure the backend is running, or point the app at a different API:

```bash
# .env.local
VITE_API_BASE=http://localhost:8080
```

## Available scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start dev server with hot-reload |
| `npm run build` | Build for production into `dist/` |
| `npm run preview` | Preview the production build locally |

## Environment variables

| Variable | Default | Description |
|----------|---------|-------------|
| `VITE_API_BASE` | `http://localhost:8080` | Base URL of the backend API |

For production, this is set to `https://api.kurang.click` via `.env.production`.

## Features

- Shorten any URL with an optional custom short code
- Set an optional expiry date/time
- One-click copy of the shortened link
- Delete a shortened link after creation

## Tech stack

- [Vue 3](https://vuejs.org/) (Composition API)
- [Vite 8](https://vite.dev/)
