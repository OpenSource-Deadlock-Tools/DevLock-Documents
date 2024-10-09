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
        DS -- Add to Ingest Queue --> RMQI
        DS -- Publish Events --> RMQPS
        
        H["Housekeeper"]
        H == Scan for unprocessed old Matches<br>Compress and Archive old Matches === S
        H -- Add to Ingest Queue --> RMQI
        
        subgraph RMQ["RabbitMQ"]
            RMQI["Ingest Queue"]
            RMQPS["Pub/Sub"]
        end
        
        V["Validator & Parser & Ingestor"]
        RMQI -- Get from Ingest Queue --> V
        S -- Match Data --> V
        V -- Parsed Match Data --> S
        
        subgraph DB["Databases"]
            direction TB
            DB1[("DBMS 1")]
            DB2[("DBMS 2")]
        end
        V -- Ingest Data --> DB
    end
    
    E["External"]
    DB == Application Data ==> E
    D == Telemetry & Metrics ==> E
    RMQPS == Game Events ==> E
```

## Data Scrapers

### HLTV-Spectator
```mermaid
flowchart BT
    subgraph M["Match Manager"]
        direction LR
        DB["Database"]
        MM["Match Manager"]
        MM === DB
    end

    subgraph SB["Spectator Bots"]
        direction TB
        SB1["Spectator Bot 1"]
        SB2["Spectator Bot 2"]
    end

    subgraph E["External"]
        direction BT
        SQ["Spectate Queue"]
    end

    M -- Matches to Spectate --> SQ
    SQ -- Match to Spectate --> SB
    SB -- ACK / NACK --> SQ
    SQ -- Failed Matches --> M
```

### User-Ingest Server
```mermaid
flowchart TB
    U["User"]
    UIA["User-Ingest API"]

    U == Match Metadata (Salts) ==> UIA

    VR["Valve Replay Server"]
    UIA -- Match Metadata (Salts) --> VR
    VR -- Match Data --> UIA


    subgraph E["External"]
        S["S3-Storage"]
        RMQI["Ingest Queue"]
    end
    UIA -- Match Data ---> S
    UIA -- Match Data ---> RMQI
```
