# Deploy WARBREAK on Vercel (Two Projects)

Use two separate Vercel projects:

- Backend API: FastAPI from the repository root, using `api/index.py`
- Frontend app: Next.js from `frontend/`

This is simpler and more predictable than Vercel Services for this repo.

## 1) Backend Project

Create a new Vercel project from this repository.

Settings:
- Root Directory: `.`
- Framework Preset: `Other`
- Build Command: leave empty/default
- Output Directory: leave empty/default
- Install Command: leave empty/default

Environment variables:
- `OPENROUTER_API_KEY` = your OpenRouter key

Files used:
- `vercel.json`
- `api/index.py`
- `requirements.txt`
- `backend/`

After deploy, copy the backend URL, for example:

```txt
https://warbreak-api.vercel.app
```

Verify:

```txt
GET /health
GET /health/startup
POST /games
POST /turn
POST /intel
GET /autopsy/{game_id}
```

`/health/startup` should return `openrouter_configured: true`.

## 2) Frontend Project

Create a second Vercel project from the same repository.

Settings:
- Root Directory: `frontend`
- Framework Preset: `Next.js`
- Build Command: default
- Output Directory: default
- Install Command: default

Environment variables:
- `NEXT_PUBLIC_API_URL` = your backend project URL, with no trailing slash

Example:

```txt
NEXT_PUBLIC_API_URL=https://warbreak-api.vercel.app
```

Do not set `OPENROUTER_API_KEY` on the frontend project.

## 3) Local Development

Backend:

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

Frontend:

```bash
cd frontend
npm install
echo "NEXT_PUBLIC_API_URL=http://localhost:8000" > .env.local
npm run dev
```

## Notes

- Backend game state is in memory. Sessions can reset on cold starts or redeploys.
- For a hackathon demo this is acceptable, but persistent storage should be added for production.
- If frontend requests fail in production, confirm `NEXT_PUBLIC_API_URL` points to the backend deployment URL.
