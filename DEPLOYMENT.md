# Deployment Guide

This repository contains a React + Vite frontend and a separate Node/Express backend with Socket.IO and MongoDB.

## Overview

- Frontend: `src/` powered by React, TypeScript, Vite, Tailwind CSS
- Backend: `backend/server.js` using Express, Socket.IO, MongoDB, JWT auth
- Frontend API & socket base URL is configured using `VITE_API_URL`

## Prerequisites

- Node.js 18+ (or compatible LTS)
- npm
- MongoDB database URL
- `backend/.env` with required environment variables
- Optional local `.env` at repository root for Vite

## Environment Variables

### Backend (`backend/.env`)

Create `backend/.env` with:

```env
MONGO_URI=mongodb+srv://<user>:<password>@<cluster>/<dbname>?retryWrites=true&w=majority
JWT_SECRET=your_jwt_secret
PORT=5000
```

### Frontend (`.env`)

Create `.env` in the repository root with:

```env
VITE_API_URL=http://localhost:5000
```

This value is used by:

- `src/services/authService.ts` for authentication API requests
- `src/services/socket.ts` for Socket.IO connections

## Local Production Build

1. Install frontend dependencies:

```bash
npm install
```

2. Install backend dependencies:

```bash
cd backend
npm install
cd ..
```

3. Build frontend:

```bash
npm run build
```

4. Start backend:

```bash
cd backend
npm start
```

5. Open the frontend in a static server or use a separate dev server for local testing.

> Note: The backend currently does not serve the frontend build output automatically. If you want a single unified host, add static file serving in `backend/server.js` or deploy the frontend separately.

## Deploying Frontend

### Option A: Static host (recommended)

Use Netlify, Vercel, Cloudflare Pages, or another static host.

- Build command: `npm run build`
- Publish directory: `dist`
- Set environment variable: `VITE_API_URL` to the backend URL

### Option B: Serve via a Node host

If you want the backend to serve the built frontend, add Express static middleware to `backend/server.js` and point it at the `dist` folder after building.

## Deploying Backend

The backend is a standalone Node service located in `backend/`.

### Common deployment hosts

- Render
- Railway
- Heroku
- Fly.io
- DigitalOcean App Platform

### Required settings

- `MONGO_URI` = MongoDB connection string
- `JWT_SECRET` = secret key for JWT signing
- `PORT` = port exposed by the host

### Start command

```bash
npm start
```

## Recommended Production Flow

1. Deploy backend to a Node host.
2. Deploy frontend to a static host or to the same host if you add static serving.
3. Configure the frontend host with `VITE_API_URL` pointing to the backend URL.
4. Confirm socket connections and auth API requests work from the deployed app.

## Validation

- Verify the app can sign up and login users.
- Confirm tasks synchronize across browser sessions via Socket.IO.
- Check the network tab for API requests to `${VITE_API_URL}/api/auth/*`.

## Additional Notes

- `npm run type-check` runs TypeScript only checks
- `npm run lint` validates frontend code with ESLint
- `npm test` executes frontend tests with Vitest

## Potential Improvements for Deployment

If you want a single-service deployment, consider adding a static file serving route in `backend/server.js`:

```js
const path = require("path");
app.use(express.static(path.join(__dirname, "../dist")));
app.get("*", (req, res) => {
  res.sendFile(path.join(__dirname, "../dist/index.html"));
});
```

Then build the frontend before starting the backend.
