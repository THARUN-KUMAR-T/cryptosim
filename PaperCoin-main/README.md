# CryptoSim - Virtual Crypto Trading Simulator

A paper trading platform for practicing crypto trading with real-time Binance market data, built with Next.js 15 and TimescaleDB.

## Features

- **Live Market Data** - Real-time BTC/USDT price, orderbook, and candlestick charts via Binance WebSocket
- **Virtual Trading** - Place limit/market orders with $10,000 virtual USDT
- **Client-Side Matching Engine** - Matches virtual orders against live Binance orderbook every 3 seconds
- **Orderbook Overlay** - Visual display showing your virtual orders alongside real market depth
- **Backtesting** - Replay historical orderbook data at configurable speed (1x-5x)
- **Portfolio Tracking** - Real-time balance, invested amount, and trade history

## Architecture

```
[Browser]
    |
    |-- React Context Providers:
    |   |-- OrderBookContext     -- Binance WS + virtual orders + merged orderbook
    |   |-- MatchingEngineContext -- 3s interval matching loop
    |   |-- BacktestContext      -- backtest on/off + config
    |
    |-- Binance WebSockets (3 connections, no auth):
    |   |-- depth20@1000ms      -- orderbook levels
    |   |-- kline_{interval}    -- candlestick data
    |   |-- ticker              -- 24h price stats
    |
[Next.js Server]
    |-- JWT Middleware           -- protects /dashboard, /profile
    |-- API Routes              -- REST endpoints for auth, trades, portfolios
    |
[PostgreSQL / TimescaleDB]
    |-- hypertables with compression + retention policies
```

## Tech Stack

- **Frontend**: Next.js 15, React 19, Tailwind CSS, shadcn/ui
- **Charts**: AmCharts5 (candlestick), Recharts (portfolio)
- **State**: Zustand (auth), React Context (trading)
- **Database**: PostgreSQL + TimescaleDB
- **Auth**: JWT (email/password + Google OAuth)
- **WebSocket**: Binance public streams (no API keys needed)

## Setup

```bash
npm install

# Create .env.local with:
# DATABASE_URL=postgresql://user:pass@host:5432/dbname
# JWT_SECRET=your_secret
# OAUTH_CLIENT_ID=your_google_client_id
# OAUTH_CLIENT_SECRET=your_google_client_secret
# NEXT_PUBLIC_APP_URL=http://localhost:3000

npm run dev
```

## Database Schema

See `tables.sql` for the full schema including:
- TimescaleDB hypertables with 1-day chunk intervals
- Auto-compression after 3-7 days
- 180-day retention policy
