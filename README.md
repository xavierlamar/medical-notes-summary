# 🩺 Healthcare Consultation Assistant (SaaS)

A **professional healthcare SaaS application** that helps doctors transform raw consultation notes into **structured medical summaries**, **clear next steps**, and **patient-friendly email drafts** — streamed in real time using AI.

Built with a **modern Next.js frontend**, **FastAPI backend**, **Clerk authentication**, and deployed on **Vercel**.

---

## ✨ Features

- ⚛️ Next.js (Pages Router) for stability
- 🟦 TypeScript for type safety
- 🔐 Clerk Authentication & subscription protection
- 🐍 FastAPI backend (Vercel Serverless)
- 🔄 Real-time AI streaming responses (SSE)
- 📝 Markdown rendering of medical summaries
- 📅 Structured consultation forms with date picker
- ☁️ Vercel-ready production deployment

---

## 🗂️ Project Structure

saas/
├─ api/
│ └─ index.py # FastAPI backend (Vercel Serverless)
├─ pages/
│ ├─ _app.tsx
│ ├─ _document.tsx
│ ├─ index.tsx # Marketing / landing page
│ └─ product.tsx # Consultation assistant (protected)
├─ public/
├─ styles/
│ └─ globals.css
├─ .env.local
├─ requirements.txt
├─ package.json
├─ next.config.ts
└─ README.md

yaml
Copy code

---

## 🩺 What This App Does

The **Healthcare Consultation Assistant** allows medical professionals to:

- Enter doctor’s consultation notes
- Select the visit date using a date picker
- Generate:
  - 🧾 **Professional summaries** for medical records
  - ✅ **Actionable next steps** for the doctor
  - 📧 **Patient-friendly email drafts**
- Stream AI-generated content in real time
- Secure access with authentication and subscriptions

---

## 🔐 Environment Variables

### Local Development (`.env.local`)

```env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_XXXXXXXX
CLERK_SECRET_KEY=sk_test_XXXXXXXX
CLERK_JWKS_URL=https://api.clerk.com/v1/jwks
⚠️ Never commit .env.local to GitHub

Vercel Environment Variables
In Vercel Dashboard → Project → Settings → Environment Variables, add:

Name	Value
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY	Clerk publishable key
CLERK_SECRET_KEY	Clerk secret key
CLERK_JWKS_URL	Clerk JWKS URL

Enable for:

Production

Preview

Development

📦 Installation (Local Development)
1️⃣ Clone the repository
bash
Copy code
git clone https://github.com/your-username/saas.git
cd saas
2️⃣ Install frontend dependencies
bash
Copy code
npm install
3️⃣ Install backend dependencies
bash
Copy code
pip install -r requirements.txt
4️⃣ Install additional dependencies (Consultation Form)
bash
Copy code
npm install react-datepicker
npm install --save-dev @types/react-datepicker
5️⃣ Run locally
Frontend (Next.js)
bash
Copy code
npm run dev
Backend (FastAPI – optional local run)
bash
Copy code
uvicorn api.index:app --reload
Open:

arduino
Copy code
http://localhost:3000
🔄 AI Streaming (SSE)
Uses Server-Sent Events (SSE)

Streams AI output from FastAPI → Next.js

Renders output incrementally as Markdown

Optimized for long-form medical summaries

🔐 Authentication & Access Control (Clerk)
Implemented with @clerk/nextjs

JWT-based authentication for API access

Subscription protection using <Protect />

Secure backend verification via Clerk JWKS

☁️ Deploying to Vercel (Production)
1️⃣ Install Vercel CLI
bash
Copy code
npm install -g vercel
2️⃣ Login to Vercel
bash
Copy code
vercel login
3️⃣ Deploy to production
bash
Copy code
vercel --prod
4️⃣ Verify deployment
✅ Frontend deployed automatically

✅ FastAPI runs via /api/index.py

✅ Environment variables loaded securely

✅ Clerk authentication & subscriptions active

✅ Real-time AI streaming operational 🎉

🛠️ Tech Stack
Next.js 16 (Pages Router)

React 19

TypeScript

FastAPI (Python)

Clerk Authentication

Tailwind CSS

Server-Sent Events (SSE)

Vercel