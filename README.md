# Leaderboard & Analytics Service

## Description
This microservice is responsible for managing leaderboards and game statistics. Its goal is to provide fast, real-time reads of scores and rankings.

## Key Responsibilities
- **Global & Personal Leaderboards**: Global rankings and filtering by friends or regions.
- **Top Scores**: Management of historical and daily/weekly high scores.
- **Statistics (Stats)**: Calculation and storage of basic statistics (e.g., average kills, playtime, accuracy).

## Suggested Tech Stack
- **Language/Framework**: Java 21 + Spring Boot 3.x
- **Database (Cache/Fast Read)**: Redis (Sorted Sets - ZADD/ZRANGE) for real-time leaderboards.
- **Messaging**: Apache Kafka (Kafka Streams) for aggregating events and calculating statistics asynchronously.

## Priority
**Medium**. This service is key to demonstrating read optimization and real-time data handling (Redis Sorted Sets), which is a strong point in technical interviews.

## Integration
- Consumes `GameEnded` events from Kafka to update scores.
- Exposes REST/WebSocket APIs for the frontend to query rankings.
