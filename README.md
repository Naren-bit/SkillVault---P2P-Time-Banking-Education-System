# SkillVault: Peer-to-Peer Time Banking System

A full-stack web application where students trade knowledge instead of money. Teach for 1 hour, earn 1 Time Credit. Spend that credit to learn something new.

## 🎯 Project Overview

SkillVault is a peer-to-peer time banking platform designed for the Build2Break hackathon. The system implements financial-grade transaction handling to prevent:

- **Double Spending**: Atomic MongoDB transactions ensure credits can't be spent twice
- **Race Conditions**: Optimistic locking prevents concurrent session modifications
- **Logic Flaws**: Server-side validation prevents negative transfers and self-transactions

## 🏗️ Architecture

```
Index/
├── backend/                 # Express.js API Server
│   ├── config/             # Database & app configuration
│   ├── controllers/        # Route handlers
│   ├── middleware/         # Auth, validation, error handling
│   ├── models/             # Mongoose schemas
│   ├── routes/             # API route definitions
│   ├── services/           # Business logic (Transactions, Sessions)
│   └── server.js           # Entry point
│
└── frontend/               # Next.js 15 Application
    └── src/
        ├── app/            # App router pages
        ├── components/     # Reusable UI components
        ├── context/        # React contexts (Auth)
        ├── lib/            # API client, utilities
        └── types/          # TypeScript definitions
```

## 🛡️ Security Features

### Transaction Service (backend/services/TransactionService.js)
- **Atomic Transfers**: Uses MongoDB transactions to ensure credits are deducted and added simultaneously
- **Optimistic Locking**: Version fields prevent concurrent balance modifications
- **Idempotency Keys**: Prevent duplicate transaction processing

### Session Service (backend/services/SessionService.js)
- **Locking Mechanism**: Temporary locks prevent race conditions on session status changes
- **Status Validation**: State machine ensures valid status transitions
- **Double-Processing Prevention**: `isProcessed` flag prevents completing a session twice

### Input Validation
- Express-validator ensures all inputs are sanitized
- Amount validation prevents negative transfers
- User validation prevents self-transactions

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- MongoDB (local or Atlas)
- npm or yarn

### Backend Setup

```bash
cd backend

# Install dependencies
npm install

# Create .env file
cp .env.example .env

# Edit .env with your MongoDB URI
# MONGODB_URI=mongodb://localhost:27017/skillvault

# Start the server
npm run dev
```

The API will be available at `http://localhost:5000`

### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Create .env.local
echo "NEXT_PUBLIC_API_URL=http://localhost:5000/api" > .env.local

# Start the development server
npm run dev
```

The app will be available at `http://localhost:3000`

## 📚 API Endpoints

### Authentication
- `POST /api/auth/register` - Create new account (grants 5 free credits)
- `POST /api/auth/login` - Login and receive JWT
- `GET /api/auth/me` - Get current user profile
- `PUT /api/auth/me` - Update profile

### Users & Marketplace
- `GET /api/users/search` - Search for tutors
- `GET /api/users/:id` - Get user public profile
- `POST /api/users/skills/teaching` - Add a teaching skill
- `DELETE /api/users/skills/teaching/:id` - Remove a skill

### Sessions
- `POST /api/sessions` - Book a session
- `GET /api/sessions` - Get user's sessions
- `PUT /api/sessions/:id/confirm` - Tutor confirms session
- `PUT /api/sessions/:id/complete` - Complete and transfer credits
- `PUT /api/sessions/:id/cancel` - Cancel a session
- `POST /api/sessions/:id/review` - Add review after completion

### Wallet
- `GET /api/wallet` - Get balance summary
- `GET /api/wallet/transactions` - Get transaction history

## 🧪 Adversarial Testing Scenarios

The system is designed to handle these attack vectors:

### 1. Double Spend Attack
Try to book two sessions simultaneously when you only have enough credits for one.
- **Protection**: Atomic transactions with balance check

### 2. Race Condition Attack
Try to complete the same session from two different browsers.
- **Protection**: Locking mechanism with `isProcessed` flag

### 3. Negative Transfer Attack
Try sending `-10` credits to steal from another user.
- **Protection**: Server-side validation rejects non-positive amounts

### 4. Self-Transaction Attack
Try to book a session with yourself to generate infinite credits.
- **Protection**: Tutor ≠ Student validation in session creation

## 🎨 Features

- **Dashboard**: View balance, stats, and upcoming sessions
- **Marketplace**: Search and filter tutors by skill
- **Session Management**: Book, confirm, complete, and cancel sessions
- **Wallet**: Track all transactions with full history
- **Profile**: Manage teaching skills and interests

## 📝 Tech Stack

**Backend:**
- Express.js
- MongoDB with Mongoose
- JWT Authentication
- Express Validator
- Helmet & CORS

**Frontend:**
- Next.js 15 (App Router)
- TypeScript
- Tailwind CSS
- Lucide Icons

## 🏆 Build2Break Ready

This project is designed for adversarial testing. Peers are encouraged to try breaking:
- The credit system
- Session booking logic
- Transaction handling
- Authentication flows

May the best hacker win! 🎯
