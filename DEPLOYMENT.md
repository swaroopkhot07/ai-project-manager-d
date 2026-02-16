# Hybrid Deployment Guide

This guide explains how to deploy the AI Project Manager application using **Option 1: Hybrid Deployment** (Vercel + Railway).

## Prerequisites

- GitHub account
- Vercel account (sign up at https://vercel.com)
- Railway account (sign up at https://railway.app)
- Gemini API key from https://ai.google.dev

---

## Part 1: Deploy Backend to Railway (~10 minutes)

### Step 1: Push Code to GitHub
Your code is already on GitHub at `swaroopkhot07/ai-project-manager-d`.

### Step 2: Create Railway Project
1. Go to https://railway.app
2. Click **"New Project"**
3. Select **"Deploy from GitHub repo"**
4. Choose `swaroopkhot07/ai-project-manager-d`
5. Railway will auto-detect Python and start building

### Step 3: Configure Environment Variables
In Railway project settings, add these environment variables:
```
GEMINI_API_KEY=your_actual_api_key_here
PORT=8000
ALLOWED_ORIGINS=https://your-frontend.vercel.app,http://localhost:4200
```

### Step 4: Configure Build Settings (if needed)
Railway should auto-detect, but you can manually set:
- **Build Command**: `pip install -r requirements.txt && python -m spacy download en_core_web_sm`
- **Start Command**: `cd backend && uvicorn main:app --host 0.0.0.0 --port $PORT`
- **Root Directory**: `/` (leave empty or set to root)

### Step 5: Get Your Backend URL
After deployment completes, Railway will provide a URL like:
```
https://your-backend-app.railway.app
```
**Copy this URL** - you'll need it for the frontend deployment.

---

## Part 2: Deploy Frontend to Vercel (~10 minutes)

### Step 1: Update Production Environment
Before deploying, update the backend URL in:
```typescript
// frontend/src/environments/environment.ts
export const environment = {
  production: true,
  apiUrl: 'https://your-backend-app.railway.app'  // Replace with your actual Railway URL
};
```

### Step 2: Commit the Change
```bash
git add frontend/src/environments/environment.ts
git commit -m "Update production API URL"
git push origin main
```

### Step 3: Deploy to Vercel
1. Go to https://vercel.com/new
2. Import your GitHub repository: `swaroopkhot07/ai-project-manager-d`
3. **Root Directory**: Set to `frontend`
4. **Framework Preset**: Angular
5. Click **"Deploy"**

Vercel will automatically:
- Install dependencies
- Build your Angular app
- Deploy to a global CDN

### Step 4: Get Your Frontend URL
Vercel will provide a URL like:
```
https://ai-project-manager-d-xxxxx.vercel.app
```

### Step 5: Update Railway CORS
Go back to Railway and update the `ALLOWED_ORIGINS` environment variable:
```
ALLOWED_ORIGINS=https://ai-project-manager-d-xxxxx.vercel.app,http://localhost:4200
```

Railway will automatically redeploy with the new CORS settings.

---

## Part 3: Testing (~5 minutes)

1. Open your Vercel frontend URL
2. Type a message like: "create project TestProj with 10 tasks, 5 to backend, 5 to frontend"
3. You should see a successful response!

### Troubleshooting

**Issue**: "Error connecting to AI"
- **Check**: Railway backend is running (check logs)
- **Check**: CORS is configured correctly
- **Check**: Environment variables are set in Railway

**Issue**: Frontend shows blank page
- **Check**: Vercel build logs for errors
- **Check**: Browser console for errors

**Issue**: API calls fail with CORS error
- **Check**: ALLOWED_ORIGINS includes your Vercel URL
- **Redeploy**: Railway backend after updating CORS

---

## Configuration Files Reference

### Created Files:
- ✅ `frontend/vercel.json` - Vercel deployment config
- ✅ `frontend/src/environments/environment.ts` - Production env
- ✅ `frontend/src/environments/environment.development.ts` - Dev env
- ✅ `railway.json` - Railway config (optional, auto-detected)
- ✅ Updated `requirements.txt` - Pinned versions
- ✅ Updated `backend/config.py` - CORS configuration
- ✅ Updated `frontend/src/app/services/chat.service.ts` - Environment-based API URL

---

## Cost Breakdown

### Railway (Backend)
- **Free Tier**: $5 credit/month
- **Usage**: ~$3-5/month for small projects
- **Upgrade**: Hobby plan $5/month for more resources

### Vercel (Frontend)
- **Free Tier**: Unlimited for personal projects
- **Bandwidth**: 100 GB/month free
- **Build minutes**: Unlimited for personal repos

### Total: **FREE** (within free tier limits)

---

## Next Steps

1. ✅ Deploy backend to Railway
2. ✅ Update environment.ts with Railway URL
3. ✅ Deploy frontend to Vercel
4. ✅ Update CORS in Railway
5. ✅ Test deployment
6. 🎉 Share your live app!

---

## Commands Summary

```bash
# Update production environment
code frontend/src/environments/environment.ts
# (Update the apiUrl with your Railway backend URL)

# Commit and push
git add .
git commit -m "Configure production deployment"
git push origin main

# That's it! Vercel and Railway will auto-deploy from GitHub
```
