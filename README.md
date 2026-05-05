# MeterFlow — Usage-Based API Billing Platform

A platform for API monetization. Track usage, enforce rate limits, calculate billing, and visualize analytics all in one stack.

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite, Tailwind CSS, Recharts |
| Backend | Node.js + Express |
| Auth | JWT (jsonwebtoken) + bcryptjs |
| SQL DB | PostgreSQL (users, billing, plans, keys) |
| NoSQL DB | MongoDB (API logs) |
| Cache / Rate Limit | Redis |
| Async Queue | RabbitMQ |
| HTTP Client | Axios |
| Scheduler | node-cron |


## 📁 Project Structure

```
meterflow/
├── backend/
│   ├── config/
│   │   ├── postgres.js       
│   │   ├── mongo.js          
│   │   ├── redis.js          
│   │   ├── rabbitmq.js       
│   │   ├── migrate.js        
│   │   └── seed.js           
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── apiKeyController.js
│   │   ├── gatewayController.js
│   │   ├── analyticsController.js
│   │   ├── billingController.js
│   │   └── adminController.js
│   ├── middleware/
│   │   ├── auth.js          
│   │   ├── apiKeyAuth.js     
│   │   ├── rateLimit.js     
│   │   └── requestLogger.js  
│   ├── models/
│   │   └── ApiLog.js         
│   ├── routes/
│   │   ├── auth.js
│   │   ├── apiKeys.js
│   │   ├── gateway.js
│   │   ├── analytics.js
│   │   ├── billing.js
│   │   └── admin.js
│   ├── services/
│   │   └── billingCron.js    
│   ├── .env.example
│   ├── package.json
│   └── server.js
│
└── frontend/
    ├── src/
    │   ├── components/
    │   │   ├── layout/
    │   │   │   ├── AppLayout.jsx
    │   │   │   └── Sidebar.jsx
    │   │   ├── ui/
    │   │   │   └── index.jsx  
    │   │   └── ProtectedRoute.jsx
    │   ├── pages/
    │   │   ├── Auth.jsx        
    │   │   ├── Dashboard.jsx
    │   │   ├── ApiKeys.jsx
    │   │   ├── Analytics.jsx
    │   │   ├── Billing.jsx
    │   │   ├── Playground.jsx
    │   │   └── Admin.jsx
    │   ├── services/
    │   │   └── api.js          
    │   ├── store/
    │   │   └── AuthContext.jsx
    │   ├── App.jsx
    │   ├── main.jsx
    │   └── index.css
    ├── index.html
    ├── vite.config.js
    ├── tailwind.config.js
    └── package.json
```

## 🏗️ Architecture Overview

```
                         ┌─────────────────┐
                         │  React Frontend │
                         │  (Port 3000)    │
                         └────────┬────────┘
                                  │ 
                         ┌────────▼─────────┐
                         │  Express Backend │
                         │   (Port 5000)    │
                         └────────┬─────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
    ┌─────────▼─────┐   ┌────────▼──────┐   ┌───────▼──────┐
    │  JWT Auth     │   │  API Gateway  │   │  Analytics   │
    │  Middleware   │   │  Middleware   │   │  Engine      │
    └───────────────┘   └────────┬──────┘   └──────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
    ┌─────────▼─────┐  ┌────────▼──────┐  ┌───────▼──────┐
    │   Redis       │  │  RabbitMQ     │  │  External    │
    │  Rate Limit   │  │  Log Queue    │  │  APIs        │
    └───────────────┘  └────────┬──────┘  └──────────────┘
                                │
              ┌─────────────────┼──────────────────┐
              │                                    │
    ┌─────────▼─────┐                   ┌─────────▼──────┐
    │  PostgreSQL   │                   │  MongoDB       │
    │  (Users,      │                   │  (API Logs)    │
    │   Billing,    │                   └─────────────── ┘
    │   Keys)       │
    └───────────────┘
```

## ✅ Prerequisites

Install these before running MeterFlow:

**macOS quick install:**
```bash
brew install postgresql@14 mongodb-community redis rabbitmq
brew services start postgresql@14
brew services start mongodb-community
brew services start redis
brew services start rabbitmq
```
## 🔧 Local Setup

### Step 1

```bash
# Backend
cd meterflow/backend
npm install

# Frontend
cd ../frontend
npm install
```

### Step 2 — Set up PostgreSQL

```bash
# Connect to PostgreSQL
psql -U postgres

# Create database
CREATE DATABASE meterflow;
\q
```

### Step 3 — Configure environment

```bash
cd backend
cp .env.example .env
# Edit .env with your DB credentials
```

### Step 4 — Run database migrations

```bash
cd backend
node config/migrate.js
```

This creates all PostgreSQL tables:
- `users`
- `plans` (Free, Starter, Pro, Enterprise)
- `user_plans`
- `api_keys`
- `invoices`
- `usage_daily`
- `webhook_events`

### Step 5 — Seed sample data (optional)

```bash
node config/seed.js
```

Creates:
- Demo user: `demo@meterflow.io` / `Demo@123456`
- 30 days of usage data
- 200 sample API logs in MongoDB
- Sample invoice

## Running the Project

Open **three terminals**:

### Terminal 1 — Backend
```bash
cd meterflow/backend
npm run dev
# Runs on http://localhost:5000
```

### Terminal 2 — Frontend
```bash
cd meterflow/frontend
npm run dev
# Runs on http://localhost:3000
```

### Terminal 3 — (Optional) External services status
```bash
# Verify services are running
redis-cli ping         
mongosh --eval "db.runCommand({ping:1})"   
psql -U postgres -c "SELECT version();"   
```

##  Sample Test Data

| Email | Password |
|---|---|
| demo@meterflow.io | Demo@123456 | 


