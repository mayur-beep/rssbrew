# RSSBrew - Railway Deployment Guide

Deploy RSSBrew on Railway with Redis support.

## Prerequisites

- A [Railway](https://railway.app) account
- A GitHub account

## Step 1: Fork or Clone the Repository

Fork this repo to your GitHub account:
```
https://github.com/mayur-beep/rssbrew
```

## Step 2: Create Railway Project

1. Go to [Railway Dashboard](https://railway.app/dashboard)
2. Click **"New Project"** → **"Deploy from GitHub repo"**
3. Select your forked `rssbrew` repository

## Step 3: Add Redis

1. In your Railway project, click **"+ New"**
2. Select **"Database"** → **"Add Redis"**
3. Wait for Redis to deploy (shows "Online")

## Step 4: Set Environment Variables

Click on your **rssbrew** service → **Variables** tab → Add these:

| Variable | Value | Required |
|----------|-------|----------|
| `SECRET_KEY` | Random string (use `openssl rand -base64 32`) | Yes |
| `DEPLOYMENT_URL` | `your-app-name.up.railway.app` | Yes |
| `REDIS_URL` | `${{Redis.REDIS_URL}}` | Yes |
| `OPENAI_API_KEY` | Your OpenAI API key | No (only for AI features) |
| `TIME_ZONE` | `UTC` or your timezone | No |

### Important Notes:
- Use `${{Redis.REDIS_URL}}` (Railway reference) - this auto-connects to your Redis service
- Do NOT set `DEBUG` in production
- `DEPLOYMENT_URL` should be without `https://`

## Step 5: Deploy

1. Click **"Deploy"** button
2. Wait for build to complete
3. Your app will be available at `https://your-app-name.up.railway.app`

## Step 6: Create Admin User

After deployment, go to your app URL and login with default credentials:
- **Username:** `admin`
- **Password:** `admin`

**Change the password immediately** in the admin panel.

## Architecture

```
┌─────────────┐     ┌─────────────┐
│   rssbrew   │────▶│    Redis    │
│  (Django)   │     │  (Huey Q)   │
└─────────────┘     └─────────────┘
```

- **rssbrew**: Main Django application
- **Redis**: Task queue for background jobs (feed updates, digest generation)

## Troubleshooting

### 500 Error when adding feeds
- Ensure `REDIS_URL` is set correctly using `${{Redis.REDIS_URL}}`
- Check Redis service is "Online"

### App not starting
- Check deployment logs in Railway
- Verify all required environment variables are set

### Feeds not updating
- Redis connection issue - verify `REDIS_URL`
- Check logs for Huey worker errors

## Optional: Custom Domain

1. Go to rssbrew service → **Settings** → **Networking**
2. Click **"Generate Domain"** or add custom domain
3. Update `DEPLOYMENT_URL` to match your domain

## Environment Variables Reference

| Variable | Description | Default |
|----------|-------------|---------|
| `SECRET_KEY` | Django secret key | Required |
| `DEPLOYMENT_URL` | Your app hostname | Required |
| `REDIS_URL` | Redis connection URL | Required |
| `OPENAI_API_KEY` | OpenAI API key for AI features | None |
| `OPENAI_BASE_URL` | Custom OpenAI-compatible API URL | `https://api.openai.com/v1` |
| `TIME_ZONE` | Application timezone | `UTC` |
| `CRON` | Feed update schedule | `0 * * * *` (hourly) |
| `CRON_DIGEST` | Digest generation schedule | `0 0 * * *` (daily) |

---

## Support

For issues, open a GitHub issue at: https://github.com/mayur-beep/rssbrew/issues
