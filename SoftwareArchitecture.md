# Software Architecture

This chart is a high-level overview of the software architecture of the project.

_It is a work in progress and will be updated as the project progresses._

# Data & Analytics

## Overview
```mermaid
flowchart TB
    subgraph D["Data & Analytics"]
        direction TB
        S["S3-Storage"]
        
        DS["
            Data Scrapers
            Active Matches, HLTV-Stream, Metadata
        "]
        DS -- Match Data --> S
        DS -- Add to Validate Queue --> RMQV
        DS -- Publish Events --> RMQPS
        
        H["Housekeeper"]
        H == Scan for unprocessed old Matches === S
        H == Compress and Archive old Matches === S
        H -- Add to Validate Queue --> RMQV
        
        subgraph RMQ["RabbitMQ"]
            RMQV["Validate Queue"]
            RMQI["Ingest Queue (Exchange)"]
            RMQPS["Pub/Sub"]
        end
        
        V["Validator & Parser"]
        RMQV -- Get from Validate Queue --> V
        S -- Match Data --> V
        V -- Parsed Match Data --> S
        V -- Add to Parser Queue --> RMQI
        
        subgraph I["Ingest Workers"]
            direction TB
            I1["DBMS 1 Ingest Worker"]
            I2["DBMS 2 Ingest Worker"]
        end
        RMQI -- Get from Ingest Queue --> I
        S -- Parsed Match Data --> I
        
        subgraph DB["Databases"]
            direction TB
            DB1["DBMS 1"]
            DB2["DBMS 2"]
        end
        I -- Ingest Data --> DB
    end
    
    E["External"]
    DB == Application Data ==> E
    D == Telemetry & Metrics ==> E
    RMQPS == Game Events ==> E
```

## Data Scrapers

### HLTV-Spectator
```mermaid
flowchart TB
    subgraph M["Match Manager"]
        direction TB
        DB["Database"]
        MM["Match Manager"]
        MM === DB
    end

    subgraph SB["Spectator Bots"]
        direction TB
        SB1["Spectator Bot 1"]
        SB2["Spectator Bot 2"]
    end

    subgraph RMQ["RabbitMQ"]
        direction BT
        EQ["Error Queue"]
        SQ["Spectate Queue"]
        SQ -- NACK --> EQ
    end

    M -- Matches to Spectate --> RMQ
    RMQ -- Match to Spectate --> SB
    SB -- ACK / NACK --> RMQ
    RMQ -- Failed Matches --> M
```
