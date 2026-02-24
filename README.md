# FINAL_YEAR_PROJECT
# QuantDesk — Deployment Guide

## Project Structure

```
quantdesk/
├── frontend/          ← React app  →  Deploy to Vercel
├── backend/           ← server.py  →  Deploy to Render (Service 1)
└── ml-backend/        ← ml_server.py → Deploy to Render (Service 2)
```

---

## Step 1 — Push to GitHub

1. Create a new repo at github.com
2. Run in terminal:
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/quantdesk.git
git push -u origin main
```

---

## Step 2 — Deploy Backend (server.py) on Render

1. Go to https://render.com → **New → Web Service**
2. Connect your GitHub repo
3. Settings:
   - **Root Directory**: `backend`
   - **Runtime**: Python 3
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn server:app`
   - **Instance Type**: Free
4. Click **Deploy**
5. Copy the URL → e.g. `https://quantdesk-api.onrender.com`

---

## Step 3 — Deploy ML Backend (ml_server.py) on Render

1. Go to Render → **New → Web Service** again
2. Same repo, different settings:
   - **Root Directory**: `ml-backend`
   - **Runtime**: Python 3
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn --timeout 300 ml_server:app`
   - **Instance Type**: Free (note: predictions will be slow ~2-5 min)
3. Click **Deploy**
4. Copy the URL → e.g. `https://quantdesk-ml.onrender.com`

> ⚠️ PyTorch build takes ~10 minutes on first deploy. Be patient!

---

## Step 4 — Deploy Frontend on Vercel

1. Go to https://vercel.com → **New Project**
2. Import your GitHub repo
3. Settings:
   - **Root Directory**: `frontend`
   - **Framework Preset**: Create React App
4. Add **Environment Variables**:
   ```
   REACT_APP_API_URL = https://quantdesk-api.onrender.com/api
   REACT_APP_ML_URL  = https://quantdesk-ml.onrender.com/api
   REACT_APP_ANTHROPIC_API_KEY = sk-ant-...your key...
   ```
5. Click **Deploy** 🚀

---

## ⚠️ Important Notes

| Issue | Details |
|-------|---------|
| Render free tier sleeps after 15min inactivity | First request takes ~30s to wake up |
| ML predictions train on each request | Each prediction takes 2-5 min on free tier |
| PyTorch CPU-only version used | Faster to install, no GPU needed |
| Anthropic API key | Required for AI chat feature in the app |

---

## Local Development

```bash
# Terminal 1 - Backend
cd backend
pip install -r requirements.txt
python server.py

# Terminal 2 - ML Backend  
cd ml-backend
pip install -r requirements.txt
python ml_server.py

# Terminal 3 - Frontend
cd frontend
npm install
npm start
```
