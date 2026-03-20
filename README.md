# PoE Stash Tracker - Real-time Wealth Monitor 💎
[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A high-performance wealth monitoring tool for Path of Exile. This application authenticates via OAuth2, consumes the official GGG API, and cross-references live market data from poe.ninja to provide real-time valuation and historical net-worth tracking.

## 🚀 Key Features
* **Secure Auth:** Official OAuth2 integration with Path of Exile.
* **Live Valuation:** Real-time stash scanning and pricing in Chaos/Divine orbs.
* **Wealth Analytics:** Historical tracking of total assets and per-tab distribution.
* **Performance-First:** Asynchronous processing with Spring WebFlux and localized caching.

## 📂 Project Documentation
Detailed documentation is located in the `/docs` folder:
* [📋 Functional Requirements & User Stories](docs/requirements.md)
* [🏗️ System Architecture & Class Diagram](docs/architecture.md)
* [🗄️ Database Schema (ERD)](docs/database.md)

## 🛠️ Tech Stack
* **Backend:** Java 21, Spring Boot (WebFlux, Security, Data JPA).
* **Database:** PostgreSQL (Historical snapshots), Redis/Caffeine (Price caching).
* **External APIs:** Path of Exile Official API, poe.ninja.

---
*Developed by Leandro Mantovani (Bunshock) - Computer Science Student at FAMAF (UNC).*