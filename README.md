# 🩺 Healthcare Consultation Assistant (SaaS)

A production-grade healthcare SaaS application that helps doctors transform raw consultation notes into structured medical summaries, clear next steps, and patient-friendly email drafts—streamed in real time using AI.

This application is deployed using Docker containers on AWS App Runner, following professional cloud deployment practices used by real engineering teams.

## ✨ Features

- ⚛️ Next.js (Pages Router, Static Export)
- 🟦 TypeScript
- 🔐 Clerk Authentication & subscription protection
- 🐍 FastAPI backend
- 🔄 Real-time AI streaming (Server-Sent Events)
- 📝 Markdown rendering of medical summaries
- 📅 Structured consultation forms with date picker
- 🐳 Dockerized full-stack application
- ☁️ AWS App Runner deployment (HTTPS, scaling, monitoring)

## 🏗️ Architecture Overview

**AWS Deployment Architecture:**

- Single Docker container
- FastAPI serves:
  - `/api/consultation` (AI streaming endpoint)
  - Static Next.js frontend (exported HTML/JS)
- Amazon ECR — Docker image registry
- AWS App Runner — Serverless container hosting
- AWS IAM — Secure access control
- CloudWatch — Logs and monitoring

Think of App Runner as "Vercel for Docker containers", but with full infrastructure control.

## 🗂️ Project Structure

```
saas/
├── pages/                  # Next.js Pages Router
├── styles/                 # Global styles
├── api/
│   └── server.py           # FastAPI backend (serves API + static frontend)
├── public/
├── .env                    # Environment variables (never commit!)
├── .dockerignore
├── Dockerfile
├── requirements.txt
├── package.json
├── next.config.ts
├── tsconfig.json
└── README.md
```

## 🩺 What This App Does

Doctors can:

- Enter consultation notes
- Select visit date
- Generate:
  - 🧾 Medical summaries (for records)
  - ✅ Actionable next steps
  - 📧 Patient-friendly email drafts
- Receive AI output streamed in real time
- Access the app securely with Clerk authentication

## 🔐 Environment Variables

**Local & AWS (.env)**

```env
# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
CLERK_JWKS_URL=https://api.clerk.com/v1/jwks

# OpenAI
OPENAI_API_KEY=sk-...

# AWS
DEFAULT_AWS_REGION=us-east-1
AWS_ACCOUNT_ID=123456789012
```

⚠️ **Never commit .env to GitHub**

## 🧠 Important Architecture Change (from Vercel)

This app does **NOT** use server-side rendering.

Instead:

- Next.js is exported as static files
- FastAPI serves both:
  - API routes
  - Static frontend
- Everything runs inside one Docker container

This simplifies AWS deployment and reduces cost.

## ⚙️ Next.js Configuration (Static Export)

**next.config.ts**

```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "export",
  images: {
    unoptimized: true,
  },
};

export default nextConfig;
```

## 🐳 Docker Configuration

### Dockerfile

```dockerfile
# Stage 1: Build frontend
FROM node:22-alpine AS frontend-builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
ARG NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY
ENV NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=$NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY
RUN npm run build

# Stage 2: Python backend
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY api/server.py .
COPY --from=frontend-builder /app/out ./static

EXPOSE 8000

HEALTHCHECK CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"

CMD ["uvicorn", "server:app", "--host", "0.0.0.0", "--port", "8000"]
```

### .dockerignore

```
node_modules
.next
.env
.env.local
.git
.vercel
README.md
```

## 🧪 Run Locally with Docker

```bash
docker build \
  --platform linux/amd64 \
  --build-arg NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY="$NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY" \
  -t consultation-app .

docker run -p 8000:8000 \
  -e CLERK_SECRET_KEY="$CLERK_SECRET_KEY" \
  -e CLERK_JWKS_URL="$CLERK_JWKS_URL" \
  -e OPENAI_API_KEY="$OPENAI_API_KEY" \
  consultation-app
```

Open: http://localhost:8000

## ☁️ Deploying to AWS App Runner (Production)

### 1️⃣ AWS Account Setup (CRITICAL)

- Create a full AWS account (NOT sandbox free tier)
- Enable MFA on root account
- Set budget alerts:
  - $1 (early)
  - $5 (warning)
  - $10 (stop)

### 2️⃣ Create IAM User

- Create user: `aiengineer`
- Attach policies:
  - `AWSAppRunnerFullAccess`
  - `AmazonEC2ContainerRegistryFullAccess`
  - `CloudWatchLogsFullAccess`
  - `IAMUserChangePassword`
  - `IAMFullAccess`

⚠️ **Never deploy using root credentials.**

### 3️⃣ Create ECR Repository

- Name: `consultation-app`
- Visibility: Private
- Region must match `DEFAULT_AWS_REGION`

### 4️⃣ Push Docker Image to ECR

```bash
aws ecr get-login-password --region $DEFAULT_AWS_REGION | \
docker login --username AWS --password-stdin \
$AWS_ACCOUNT_ID.dkr.ecr.$DEFAULT_AWS_REGION.amazonaws.com

docker build \
  --platform linux/amd64 \
  --build-arg NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY="$NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY" \
  -t consultation-app .

docker tag consultation-app:latest \
$AWS_ACCOUNT_ID.dkr.ecr.$DEFAULT_AWS_REGION.amazonaws.com/consultation-app:latest

docker push \
$AWS_ACCOUNT_ID.dkr.ecr.$DEFAULT_AWS_REGION.amazonaws.com/consultation-app:latest
```

⚠️ **Apple Silicon users MUST use `--platform linux/amd64`**

### 5️⃣ Create App Runner Service

- Source: Amazon ECR
- Image: `consultation-app:latest`
- Port: `8000`
- vCPU: `0.25`
- Memory: `0.5 GB`
- Min / Max instances: `1`
- Environment variables:
  - `CLERK_SECRET_KEY`
  - `CLERK_JWKS_URL`
  - `OPENAI_API_KEY`
- Health check:
  - Path: `/health`

## 💰 Cost Expectations

| Service     | Cost          |
|-------------|---------------|
| App Runner  | ~$5/month     |
| ECR         | ~$0.10/GB     |
| **Total**   | **~$5–6/month** |

Pause the service when not in use to save money.

## 🧠 Monitoring & Debugging

- App Runner → Logs tab
- CloudWatch for detailed logs
- Health endpoint: `/health`

**Common issues:**

- Missing environment variables
- Wrong port (must be 8000)
- Missing `--platform linux/amd64`

## 🚀 What You've Accomplished

- ✅ Dockerized a full-stack SaaS
- ✅ Deployed to AWS App Runner
- ✅ Enabled HTTPS, scaling, monitoring
- ✅ Set up cost controls
- ✅ Used professional cloud deployment practices
