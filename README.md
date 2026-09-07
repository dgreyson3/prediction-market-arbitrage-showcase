# Cross-Venue Prediction Market Arbitrage Engine

**A Python system for matching and evaluating sports and esports prediction-market contracts across Kalshi and Polymarket.**

**Active development** · **2026**

> **Source code is private.** This repository is a project showcase for the engineering, architecture, and behavior of the system without publishing proprietary matching, sizing, risk, or execution logic.

---

## What It Does

The engine connects to **Kalshi** and **Polymarket**, converts venue-specific market data into a shared internal model, matches equivalent binary contracts, and evaluates cross-venue opportunities using executable order-book depth.

It accounts for:

- Order-book depth and available liquidity
- Venue fees and modeled slippage
- Market-data freshness
- Capital and risk checks
- Cross-venue contract compatibility

The system supports:

- **Read-only scanning**
- **Discord-assisted manual review**
- **Paper execution**
- **Deliberately gated live execution**, disabled by default

The project is independent and is not affiliated with Kalshi or Polymarket.

---

## Demo Walkthrough

> **About the screenshots:** The views below use **synthetic data** and a **presentation-only interface generated with Codex** to make the engine's workflow easy to understand.  
> The underlying market integrations, normalization, matching, order-book analysis, persistence, execution logic, and tests are part of my project. The demo interface is not the production/operator UI and no real orders are represented in these screenshots.

### 1. System Overview

The engine tracks normalized cross-venue market pairs, maintains live order-book data, and monitors feed health before evaluating candidates.

![System overview](./screenshots/01-dashboard.png)

### 2. Cross-Venue Market Matching

Kalshi and Polymarket describe equivalent contracts differently. The engine normalizes venue-specific fields into a shared representation before approving a match.

![Matched Kalshi and Polymarket market](./screenshots/02-matched-market.png)

### 3. Depth-Aware Opportunity Analysis

The engine evaluates **executable ask depth**, not just headline or last-trade prices. It walks both books to determine a shared executable size and evaluates the paired cost after modeled fees and slippage.

![Order-book depth and opportunity analysis](./screenshots/03-opportunity-analysis.png)

### 4. Paper Execution and Safety Checks

Paper mode simulates the two-leg workflow without submitting venue orders. The execution path revalidates books, checks capital, simulates both legs, and verifies that the final result is paired.

![Paper execution workflow](./screenshots/04-execution.png)

### 5. Automated Testing

The project includes automated tests for pricing, normalization, matching, venue integrations, WebSocket behavior, risk checks, paper execution, live-execution logic, and failure handling.

**Current test run: 225 passing tests.**

![Pytest results showing 225 passing tests](./screenshots/05-tests.png)

---

## Engineering Highlights

### Cross-Venue Normalization

Venue adapters translate different Kalshi and Polymarket payloads into shared market, outcome, and order-book models.

This keeps downstream matching and pricing logic independent of either venue's raw API structure.

### Deterministic Contract Matching

The matcher reconciles differences in team names, market structure, outcome orientation, and contract metadata to identify compatible binary markets.

The exact matching rules remain private.

### Async Market Data

The system uses asynchronous REST requests and WebSocket feeds to retrieve market catalogs and maintain live order books across both venues.

Bounded concurrency, reconnect behavior, caching, and REST fallback help keep the data pipeline responsive and recoverable.

### Depth-Aware Pricing

Instead of comparing only the best displayed prices, the engine walks available order-book levels to determine how much size can actually be executed across both venues.

The evaluation includes modeled fees, slippage, freshness, liquidity, and configurable risk requirements.

### Guarded Execution

Live execution is opt-in and disabled by default.

Before any submission, the system can revalidate books, simulate the two-leg fill, verify capital, and construct fill-or-kill limit orders.

The two initial legs are submitted concurrently. Unequal fills enter rescue/unwind handling, and unresolved unmatched exposure halts further live execution.

### Persistence and Observability

PostgreSQL stores market observations, order-book snapshots, match decisions, opportunities, alerts, paper trades, and operational events.

The project also includes terminal views, a read-only local dashboard, Discord alerts, HTML/CSV reporting, and feed/system diagnostics.

---

## Tech Stack

**Language:** Python 3.12+

**APIs & Concurrency:** HTTPX · WebSockets · asyncio  
**Data & Validation:** Pydantic · SQLAlchemy · PostgreSQL · Psycopg · Alembic  
**Reliability:** Tenacity · bounded concurrency · reconnect logic · REST fallback  
**Interfaces:** Typer · Rich · Discord alerts · HTML/CSV reports  
**Testing & Quality:** Pytest · pytest-asyncio · Ruff · MyPy  
**Local Infrastructure:** Docker Compose for PostgreSQL

---

## Example Development Measurements

A completed audit scan:

- **73** normalized markets
- **642** candidate pairs
- **32** approved cross-venue matches
- **59.72 seconds** for the completed discovery/matching sample

These are development measurements, not claims of production capacity, uptime, or profitability.

---

## Project Status

This is an **active-development local system**, not a continuously deployed production trading service.

It has been validated against live market data through read-only, paper, and limited guarded-execution runs.

I do **not** present it as:

- Guaranteed or risk-free arbitrage
- A demonstrated profitable trading strategy
- A production-grade unattended trading service
- A machine-learning matching system

---

## What Stays Private

The working repository remains private because it contains strategy and operational details that are not necessary to demonstrate the engineering.

This showcase does not publish:

- Source code
- API keys, signing keys, or credentials
- Account IDs, balances, positions, or raw trade history
- Exact arbitrage thresholds
- Exact contract-matching logic
- Position-sizing logic
- Risk-limit values
- Execution timing and recheck policy
- Production configuration
- Full operational database schema

---

## About Me

**Greyson Denison-Fischer**  
Computer Science student at Central Michigan University · Graduating May 2027

[LinkedIn](https://www.linkedin.com/in/greyson-fischer/) · [GitHub](https://github.com/dgreyson3)
