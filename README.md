# Hyperledger Mortgage UI

A frontend for visualizing real-time blockchain ledger data from a Hyperledger Fabric network. Connects to three fabric channels — Records, Lending, and Books — and displays transaction blocks as expandable Material cards.

Part of a larger mortgage exchange system where the blockchain tracks every step of the mortgage lifecycle. This UI is the read layer that lets you browse transaction blocks across channels, inspect their payloads, and understand the ledger state.

## What it does

- **Channel switching** — Three buttons at the top let you toggle between blockchain channels: Records (property registry), Lending (loan lifecycle), and Books (title transfers)
- **Live ledger data** — Pulls block data from a Hyperledger Fabric REST API running on localhost:5000
- **Expandable cards** — Each transaction block renders as a Material card with the block key as header and the full JSON record collapsible inside
- **Channel icons** — Each channel type gets a corresponding institution icon (bank for lending, registry for records, title for books)

## Architecture

```
┌─────────────────────────┐         ┌──────────────────────────┐
│   Angular App (:3000)   │         │  Hyperledger Fabric API  │
│                         │  HTTP   │       (:5000)            │
│  [Records] [Lending]    │────────►│                          │
│    [Books]              │         │  GET /records            │
│                         │◄────────│  GET /lending            │
│  ┌───────────────────┐  │  JSON   │  GET /books              │
│  │ Material Card     │  │         │                          │
│  │ Key: "block_001"  │  │         │  Hyperledger Fabric      │
│  │ Record: { ... }   │  │         │  Peer nodes + Orderer    │
│  └───────────────────┘  │         └──────────────────────────┘
│  ┌───────────────────┐  │
│  │ Material Card     │  │
│  │ Key: "block_002"  │  │
│  │ Record: { ... }   │  │
│  └───────────────────┘  │
└─────────────────────────┘
```

## Tech Stack

- **Angular 5** with TypeScript
- **Angular Material** — Cards, buttons, toolbar
- **RxJS** — Observable-based HTTP calls
- **Hyperledger Fabric REST API** — Backend serving ledger channel data

## Project Structure

```
src/app/
├── app.component.*      # Main view — channel buttons + card grid
├── app.module.ts        # Module with Material imports
├── data.service.ts      # HTTP service — GET /records, /lending, /books
├── images.ts            # Maps channel icons to institution logos
└── queryResult.ts       # Block data model (Key + Record)

src/assets/
└── *.svg, *.png         # Institution logos (bank, FICO, appraiser, registrar)
```

## API Contract

The app expects a Hyperledger Fabric REST API on `localhost:5000` returning:

```json
[
  {
    "Key": "block_001",
    "Record": {
      "CustID": "C001",
      "RealEstateID": "RE100",
      "LoanAmount": "350000",
      "Status": "Approved",
      "TransactionHistory": "2018-11-15T10:30:00Z"
    }
  }
]
```

Three endpoints: `GET /records`, `GET /lending`, `GET /books`

## Running it

```bash
npm install
npm start              # http://localhost:3000
```

Requires the Hyperledger Fabric REST API running on port 5000 with the three channel endpoints.

## Related

The static version of this UI is in the [mortgage-ledger-ui](https://github.com/ArghaRay00/mortgage-ledger-ui) repo — that one reads from a JSON file instead of a live Fabric network.

## Note

This was part of a mortgage exchange blockchain project from 2018. Angular 5 is outdated but the pattern of consuming blockchain ledger data through a REST API and rendering it in a card-based UI is what this demonstrates.
