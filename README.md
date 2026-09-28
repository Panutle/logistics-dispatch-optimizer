# Autonomous Multi-Constraint Logistics Dispatch & Dynamic Telemetry Engine

[![n8n](https://img.shields.io/badge/Orchestrator-n8n-EA4B71?style=flat-square&logo=n8n)](https://n8n.io/)
[![JavaScript](https://img.shields.io/badge/Core-JavaScript%20ES6+-F7DF1E?style=flat-square&logo=javascript)](https://developer.mozilla.org/)
[![Google Sheets API](https://img.shields.io/badge/Storage-Google%20Sheets%20API-34A853?style=flat-square&logo=googlesheets)](https://developers.google.com/sheets/api)
[![LINE Messaging API](https://img.shields.io/badge/Alerts-LINE%20Messaging%20API-00C300?style=flat-square&logo=line)](https://developers.line.biz/)
[![Status](https://img.shields.io/badge/Status-Production%20Active-success?style=flat-square)]()

An automated, event-driven logistics planning and dispatch telemetry engine built with n8n and modern JavaScript algorithms. The system solves the Vehicle Routing and Bin-Packing Problem (VRP/BPP) across heterogeneous bottle packaging inventories, optimizes vehicle selection under cost and driver labor constraints, and pushes real-time shift manifests to field operations via LINE Messaging API.

---

## 📌 Architectural Overview

```mermaid
flowchart TD
    subgraph DataIngestion["1. Multi-Source Ingestion & Constraint Joining"]
        A1[Google Sheets: FM-DSM-001 Orders] --> M1[Wait for All Sheet Data - Merge Node]
        A2[Google Sheets: FM-DLW-008 Historical Route Constraints] --> M1
        A3[Google Sheets: SD-DLW-006 Cost Matrix Fuel, Labor, Toll] --> M1
        A4[Google Sheets: CAR_DATA Available Fleet] --> M1
    end

    subgraph OptimizationEngine["2. Multi-Trip Bin Packing & Cost Engine"]
        M1 --> B1[Volume Extraction & Bottle-to-Pack Normalizer]
        B1 --> B2[Dynamic Vehicle Allocation & Fleet Constraint Guard]
        B2 --> B3[Slot Allocator: 08:00-12:00 & 13:00-17:00 Fixed Windows]
        B3 --> C1[(Upsert DELIVERY_PLAN Master Sheet)]
    end

    subgraph DispatchTelemetry["3. Shift Reporting & Field Telemetry"]
        C1 --> D1[Next-Day Delivery Query Cron Mon-Sat 08:00]
        D1 --> D2[Trip Manifest & Packing Slip Aggregator]
        D2 --> D3[LINE Push Broadcast: Driver & Route Manifest]
    end

    subgraph Observability["4. Resilient Multi-Channel Error Handling"]
        E1[Execution Error Trigger] --> F1[ntfy.sh High-Priority Alert]
        E1 --> F2[LINE Bot Push Error Notification]
    end