# 🚀 EduMaster Deployment Guide (Using Neon Database)

Complete step-by-step guide to deploy EduMaster using your existing Neon PostgreSQL database.

---

## 📋 Prerequisites

- [x] GitHub account with code pushed
- [x] **Neon PostgreSQL database** (already have ✅)
- [x] Render account (free tier)
- [x] Vercel account (free tier)

---

## Part 1: Get Your Neon Database URL

### Step 1: Get Connection String from Neon

1. Go to [https://console.neon.tech](https://console.neon.tech)
2. Select your project
3. Go to **Dashboard** → **Connection Details**
4. Copy the **Connection string** (it looks like):
   ```
   postgresql://username:password@ep-xxx-xxx.us-east-2.aws.neon.tech/neondb?sslmode=require
   ```
5. **Keep this handy** - you'll need it for Render!

---

## Part 2: Deploy Backend to Render

### Step 1: Create Render Account

1. Go to [https://render.com](https://render.com)
2. Click **"Get Started"**
3. Sign up with GitHub (recommended)

### Step 2: Deploy Backend Service

1. On Render dashboard, click **"New +"** → **"Web Service"**

2. **Connect GitHub Repository:**
   - Click **"Connect GitHub"**
   - Authorize Render to access your repositories
   - Select: `Brainstorm-collab/Edumaster`
   - Click **"Connect"**

3. **Configure Service:**
   ```
   Name: edumaster-backend
   Region: Choose closest to you (e.g., Oregon USA)
   Branch: main
   Root Directory: backend
   Runtime: Node
   Build Command: npm install && npx prisma generate && npx prisma migrate deploy
   Start Command: node server.js
   Instance Type: Free
   ```

4. **Add Environment Variables** (Click "Advanced"):
   
   Click **"Add Environment Variable"** for each:
   
   ```
   DATABASE_URL
   Value: <paste your Neon connection string>
   
   JWT_SECRET
   Value: edumaster-super-secret-jwt-key-2024-change-this
   
   NODE_ENV
   Value: production
   
   PORT
   Value: 5000
   ```

5. Click **"Create Web Service"**

6. **Wait for deployment** (5-10 minutes)
   - Watch the logs for any errors
   - Once you see "Server running on port 5000" - it's ready!

7. **Copy your backend URL:**
   - Will be something like: `https://edumaster-backend.onrender.com`
   - Click the URL at the top to copy it

### Step 3: Run Database Migrations & Seed

1. In Render dashboard, go to your **edumaster-backend** service
2. Click **"Shell"** tab (top right)
3. Run these commands one by one:

   ```bash
   # Generate Prisma Client
   npx prisma generate
   
   # Run migrations
   npx prisma migrate deploy
   
   # Seed the database
   npx prisma db seed
   ```

4. Wait for each to complete
5. You should see success messages

### Step 4: Test Backend

Visit: `https://your-backend-url.onrender.com/api/health`

Should return: `{"status":"ok"}`

---

## Part 3: Deploy Frontend to Vercel

### Step 1: Create Vercel Account

1. Go to [https://vercel.com](https://vercel.com)
2. Click **"Sign Up"**
3. Sign up with GitHub (recommended)

### Step 2: Deploy Frontend

1. Click **"Add New..."** → **"Project"**

2. **Import Repository:**
   - Click **"Import Git Repository"**
   - Find and select: `Brainstorm-collab/Edumaster`
   - Click **"Import"**

3. **Configure Project:**
   ```
   Framework Preset: Vite
   Root Directory: ./ (leave as default)
   Build Command: npm run build
   Output Directory: dist
   Install Command: npm install
   ```

4. **Add Environment Variable:**
   - Click **"Environment Variables"**
   - Add variable:
     ```
     Name: VITE_API_URL
     Value: https://your-backend-url.onrender.com/api
     ```
     ⚠️ **Important**: Replace with YOUR actual Render backend URL!
     ⚠️ **Don't forget** `/api` at the end!

5. Click **"Deploy"**

6. **Wait for deployment** (3-5 minutes)

7. **Get your frontend URL:**
   - Will be something like: `https://edumaster-abc123.vercel.app`
   - Copy this URL!

---

## Part 4: Update Backend CORS

### Important: Allow Your Frontend Domain

1. Go back to **Render dashboard**
2. Click on your **edumaster-backend** service
3. Click **"Environment"** tab
4. Click **"Add Environment Variable"**:
   ```
   Name: FRONTEND_URL
   Value: https://your-vercel-url.vercel.app
   ```
   ⚠️ Replace with YOUR actual Vercel URL (no trailing slash)

5. Click **"Save Changes"**
6. Service will automatically redeploy (1-2 minutes)

---

## 🎉 Testing Your Deployment

### Test Backend
1. Visit: `https://your-backend-url.onrender.com/api/health`
2. Should see: `{"status":"ok"}`

### Test Frontend
1. Visit: `https://your-vercel-url.vercel.app`
2. Should see the EduMaster homepage
3. Try logging in:
   - **Student**: `student0@edumaster.com` / `password123`
   - **Instructor**: `john@edumaster.com` / `password123`
   - **Admin**: `admin@edumaster.com` / `password123`

---

## 🔧 Troubleshooting

### "Database connection failed"
- Check your Neon database is running
- Verify DATABASE_URL is correct (copy-paste again)
- Ensure connection string includes `?sslmode=require`

### "CORS error" in browser
- Verify FRONTEND_URL is set in Render
- Make sure no trailing slash in FRONTEND_URL
- Check backend redeployed after adding FRONTEND_URL

### "Module not found" errors
- Check build logs in Render/Vercel
- Ensure all dependencies are in package.json
- Try manual redeploy

### Backend is slow/sleeping
- Render free tier sleeps after 15 min of inactivity
- First request after sleep takes 30-60 seconds
- Upgrade to paid tier ($7/month) to prevent sleeping

---

## 📱 Optional: Custom Domain

### For Frontend (Vercel)
1. Go to Project Settings → Domains
2. Add your domain (e.g., `edumaster.com`)
3. Follow DNS setup instructions
4. Update FRONTEND_URL in Render to your custom domain

### For Backend (Render)
1. Settings → Custom Domain
2. Add your API subdomain (e.g., `api.edumaster.com`)
3. Update VITE_API_URL in Vercel

---

## 🔄 Automatic Deployments

Both platforms are now set up for **continuous deployment**:
- Push to GitHub `main` branch → Auto-deploys to production
- Check deployment status in respective dashboards
- View logs for any errors

---

## 💰 Cost Breakdown

### Current Setup (FREE)
- **Neon PostgreSQL**: Free tier (0.5 GB storage)
- **Render**: Free tier (750 hours/month)
- **Vercel**: Free tier (100 GB bandwidth)

### If You Need More
- **Neon Pro**: $19/month (unlimited storage)
- **Render Starter**: $7/month (no sleeping)
- **Vercel Pro**: $20/month (more bandwidth)

---

## 🎯 Your Deployment URLs

After deployment, save these:

```
Frontend: https://your-app.vercel.app
Backend: https://your-backend.onrender.com
Database: Neon (already set up ✅)
```

---

## 🎊 Congratulations!

Your EduMaster LMS is now **LIVE** on the internet! 🚀

Share your app with the world! 🌍
