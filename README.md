# Leaderboard & Analytics Service

## Overview
This microservice is the central hub for real-time rankings and game statistics in the **Abadox NES Remake** project. It is designed to handle high-throughput read operations with low latency, ensuring that players see updated scores instantly.

## Architecture & Responsibilities
As defined in the microservices architecture:
- **Global & Personal Leaderboards**: Manages global top scores, daily/weekly rankings, and friend leaderboards.
- **Analytics Aggregation**: Computes basic game statistics (e.g., average kills, playtime, accuracy) asynchronously.
- **High Performance**: Decoupled from the main game loop via Kafka, using Redis for sub-millisecond data retrieval.

### Interaction Flow
- **Input**: Consumes `score-updates` and `GameEnded` events from **Apache Kafka**.
- **Storage**: Uses **Redis (Sorted Sets)** as the primary data structure for rankings.
- **Output**: Exposes REST APIs consumed by the **Frontend (Phaser 3)** and **Admin Dashboard**.

## Tech Stack
- **Language**: Java 21 (leveraging Virtual Threads for I/O bound tasks).
- **Framework**: Spring Boot 3.x.
- **Database**: Redis (Key-Value & Sorted Sets).
- **Messaging**: Apache Kafka (Consumer Groups).
- **Build**: Gradle.

## Key Features (Implementation Goals)
1.  **Redis Sorted Sets (`ZADD`, `ZRANGE`)**:
    -   Efficiently store and retrieve top N players.
    -   Retrieve a specific player's rank and "neighbors" (players immediately above/below).
2.  **Event-Driven Updates**:
    -   Scores are not updated via direct REST calls from the client (to prevent cheating).
    -   Listens to `GameEnded` events produced by the **Game Core Service**.
3.  **Temporal Leaderboards**:
    -   Support for Daily, Weekly, and All-Time high scores using time-to-live (TTL) or specific Redis keys (e.g., `leaderboard:daily:2023-10-27`).

## API Endpoints (Draft)

### Public API (Player)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/leaderboards/global` | Get top X global scores (cached). |
| `GET` | `/api/leaderboards/daily` | Get top X scores for the current day. |
| `GET` | `/api/leaderboards/me` | Get current user's rank and score. |

### Internal / Admin API
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/admin/stats` | Retrieve aggregated game stats (total games, avg score). |

## Data Model Strategy (Redis)
- **Key**: `leaderboard:global` (Sorted Set)
    - **Score**: Game Points (Double)
    - **Member**: `userId` or `username`
- **Key**: `leaderboard:daily:{date}` (Sorted Set)
    - Expire after 24h + buffer.

## Learning Objectives
- Mastering **Redis Data Structures** for real-world gaming use cases.
- Implementing **Kafka Consumers** in Spring Boot.
- Designing **Scalable Read APIs** capable of handling traffic spikes.
