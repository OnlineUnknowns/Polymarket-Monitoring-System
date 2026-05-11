<div align="center">

<!-- Animated Wave Header -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=180&section=header&text=Polymarket%20Monitoring%20System&fontSize=48&fontColor=fff&animation=twinkling&fontAlignY=40" width="100%"/>

<!-- Typing Animation -->
<img src="https://readme-typing-svg.herokuapp.com?font=Fira+Code&size=22&pause=1000&color=00F7FF&center=true&vCenter=true&width=800&lines=Polymarket+Monitoring+System;Real-time+Order+Book+%7C+WebSocket+Engine;Automated+Market+Making+on+Polygon;Python+%2B+Node.js+%7C+Async+%7C+Google+Sheets;Fast+%7C+Reliable+%7C+Configurable" />

<br/>

<!-- Tech Stack Badges -->
<p>
  <img src="https://img.shields.io/badge/Python-3.9%2B-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Node.js-16%2B-339933?style=for-the-badge&logo=nodedotjs&logoColor=white"/>
  <img src="https://img.shields.io/badge/Polygon-Blockchain-8247E5?style=for-the-badge&logo=polygon&logoColor=white"/>
  <img src="https://img.shields.io/badge/WebSocket-Live-00C853?style=for-the-badge&logo=socketdotio&logoColor=white"/>
</p>
<p>
  <img src="https://img.shields.io/badge/Pandas-Data-150458?style=for-the-badge&logo=pandas&logoColor=white"/>
  <img src="https://img.shields.io/badge/Google%20Sheets-Config-34A853?style=for-the-badge&logo=googlesheets&logoColor=white"/>
  <img src="https://img.shields.io/badge/uv-Package%20Manager-DE5D43?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge"/>
</p>

<!-- Social Links -->
<p>
  <a href="https://buymeacoffee.com/onlineunknowns">
    <img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support-FFDD00?style=for-the-badge&logo=buymeacoffee&logoColor=black"/>
  </a>
  <a href="https://wa.me/201286016083">
    <img src="https://img.shields.io/badge/WhatsApp-Chat-25D366?style=for-the-badge&logo=whatsapp&logoColor=white"/>
  </a>
  <a href="https://www.linkedin.com/in/onlineunknown/">
    <img src="https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white"/>
  </a>
</p>

</div>

---

> [!WARNING]
> **In today's market, this bot is not profitable and will lose money.** Use it as a reference implementation for building your own strategies, not as a ready-to-deploy solution. Given the increased competition on Polymarket, don't engage unless you're willing to dedicate significant time and capital.

---

## 📖 Overview

**Polymarket Monitoring System** is a fully automated market-making and monitoring bot for [Polymarket](https://polymarket.com) prediction markets. It provides liquidity on both sides of the order book with configurable risk parameters, real-time WebSocket feeds, and Google Sheets-based configuration.

---

## ✨ Features

<table>
<tr>
<td>

**🔄 Real-time Data**
- Live order book via WebSocket
- Automatic reconnection & fault tolerance
- Market + user event streams

</td>
<td>

**📊 Position Management**
- Automated position merging
- Risk controls & spread management
- Stale trade detection & cleanup

</td>
</tr>
<tr>
<td>

**⚙️ Flexible Configuration**
- Google Sheets-driven parameters
- Per-market hyperparameter tuning
- Environment variable overrides

</td>
<td>

**🔍 Market Discovery**
- Full Polymarket market scanner
- Volatility & reward scoring
- Auto-sync to Google Sheets

</td>
</tr>
</table>

---

## 🏗️ System Architecture

> How all modules connect together

```mermaid
graph TB
    subgraph EXTERNAL["🌐 External"]
        PM[("Polymarket API\nREST + WebSocket")]
        BC[("Polygon\nBlockchain")]
        GS[("Google Sheets\nConfig + Stats")]
    end

    subgraph CORE["⚙️ Core — poly_data"]
        PC["PolymarketClient\npolymarket_client.py"]
        GS2["Global State\nglobal_state.py"]
        WS["WebSocket Handlers\nwebsocket_handlers.py"]
        DP["Data Processing\ndata_processing.py"]
        DU["Data Utils\ndata_utils.py"]
    end

    subgraph DAEMONS["🤖 Daemons"]
        MAIN["main.py\nMarket-Making Engine"]
        FMR["find_markets_runner.py\nMarket Discovery"]
        US["update_stats.py\nAccount Stats"]
    end

    subgraph UTILS["🛠️ Utilities"]
        PU["poly_utils\ngoogle_utils.py"]
        PS["poly_stats\naccount_stats.py"]
        PMG["poly_merger\nmerge.js  Node.js"]
        DA["data_updater\nfind_markets.py"]
    end

    PM -->|"Market Events"| WS
    PM -->|"REST Calls"| PC
    BC -->|"On-chain Data"| PC
    GS -->|"Markets + Params"| PU

    WS --> DP
    DP --> GS2
    DU --> GS2
    PC --> DU

    GS2 --> MAIN
    PU --> MAIN
    PU --> FMR
    PU --> US

    MAIN -->|"Place / Cancel Orders"| PC
    MAIN -->|"Merge Positions"| PMG
    PMG -->|"On-chain TX"| BC
    FMR --> DA
    DA -->|"Write Markets"| GS
    US --> PS
    PS -->|"Write Stats"| GS

    style EXTERNAL fill:#1a1a2e,color:#fff,stroke:#00f7ff
    style CORE fill:#16213e,color:#fff,stroke:#00f7ff
    style DAEMONS fill:#0f3460,color:#fff,stroke:#00f7ff
    style UTILS fill:#533483,color:#fff,stroke:#00f7ff
```

---

## 🔄 Trading Lifecycle

> What happens from market event to order placement

```mermaid
sequenceDiagram
    participant PM as 🌐 Polymarket WS
    participant WH as WebSocket Handler
    participant DP as Data Processing
    participant GS as Global State
    participant TE as Trading Engine
    participant PC as Polymarket Client
    participant BC as 🔗 Polygon Chain

    PM->>WH: Market event (book / price_change)
    WH->>DP: process_data(json)
    DP->>GS: Update order book
    DP->>TE: perform_trade(market) [async task]

    TE->>GS: Read position + orders + params
    TE->>TE: Calculate bid / ask prices
    TE->>PC: cancel_all_asset() if needed
    PC->>PM: Cancel request

    TE->>PC: create_order(price, size, side)
    PC->>PM: POST /order
    PM-->>WH: User event (MATCHED)
    WH->>DP: process_user_data()
    DP->>GS: Update position (optimistic)

    PM-->>WH: User event (CONFIRMED)
    WH->>DP: remove_from_performing()

    Note over TE,BC: If YES + NO positions exist
    TE->>BC: merge_positions() via Node.js
    BC-->>TE: TX hash ✅
```

---

## 🔍 Market Discovery Pipeline

> How new markets are found and synced to Google Sheets

```mermaid
flowchart LR
    A([▶ find_markets_runner.py]) --> B[get_clob_client]
    B --> C[get_all_markets\nfetch all Polymarket markets]
    C --> D[get_all_results\nfetch orderbook data]
    D --> E[get_markets\nscore + filter markets]
    E --> F[add_volatility_to_df\ncalculate volatility metrics]
    F --> G{Market count\n> threshold?}
    G -->|No ⚠️| H([Skip — data incomplete])
    G -->|Yes ✅| I[Sort by GM reward]
    I --> J[Write → All Markets sheet]
    I --> K[Filter low volatility\nWrite → Volatility Markets sheet]
    I --> L[Write → Full Markets sheet]
    J & K & L --> M([⏱ Sleep 1 hour → Repeat])

    style A fill:#0f3460,color:#fff
    style H fill:#c0392b,color:#fff
    style M fill:#0f3460,color:#fff
    style G fill:#533483,color:#fff
```

---

## 🛠️ Tech Stack

<div align="center">

| Layer | Technology |
|---|---|
| **Language** | ![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white) ![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black) |
| **Async Runtime** | ![asyncio](https://img.shields.io/badge/asyncio-WebSockets-3776AB?style=flat-square&logo=python&logoColor=white) |
| **Blockchain** | ![Polygon](https://img.shields.io/badge/Polygon-8247E5?style=flat-square&logo=polygon&logoColor=white) ![Web3](https://img.shields.io/badge/Web3.py-F16822?style=flat-square&logo=web3dotjs&logoColor=white) |
| **Data** | ![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat-square&logo=pandas&logoColor=white) |
| **Config** | ![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=flat-square&logo=googlesheets&logoColor=white) |
| **Package Manager** | ![uv](https://img.shields.io/badge/uv-DE5D43?style=flat-square&logo=python&logoColor=white) ![npm](https://img.shields.io/badge/npm-CB3837?style=flat-square&logo=npm&logoColor=white) |

</div>

---

## 🚀 Installation

### Prerequisites

- Python **3.9.10+**
- Node.js **16+**
- Google Sheets API credentials
- Polymarket account with at least one trade via the UI

### 1 — Install UV

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### 2 — Clone & install dependencies

```bash
git clone https://github.com/yourusername/polymarket-monitoring-system.git
cd polymarket-monitoring-system

# Python dependencies
uv sync

# Node.js dependencies (position merger)
cd poly_merger && npm install && cd ..
```

### 3 — Environment setup

```bash
cp .env.example .env
```

Edit `.env`:

```env
PK=your_polygon_private_key
BROWSER_ADDRESS=your_wallet_address
SPREADSHEET_URL=your_google_sheet_url
BROWSER_WALLET=your_browser_wallet_address
```

> ⚠️ Your wallet must have completed **at least one trade via the Polymarket UI** before the API permissions work correctly.

### 4 — Google Sheets setup

1. Create a **Google Service Account** and download `credentials.json` to the project root
2. Copy the [sample Google Sheet](https://docs.google.com/spreadsheets/d/1Kt6yGY7CZpB75cLJJAdWo7LSp9Oz7pjqfuVWwgtn7Ns/edit?gid=1884499063#gid=1884499063)
3. Share the sheet with your service account (Editor permission)
4. Set `SPREADSHEET_URL` in `.env`

---

## ▶️ Usage

```bash
# Start the market-making daemon
uv run python main.py

# Run the market discovery daemon (separate process / IP recommended)
uv run python find_markets_runner.py

# One-shot account stats update
uv run python update_stats.py
```

### Google Sheets configuration

| Sheet | Purpose |
|---|---|
| **Selected Markets** | Markets you want to trade |
| **All Markets** | Full Polymarket database (auto-updated) |
| **Volatility Markets** | Filtered low-volatility opportunities |
| **Hyperparameters** | Per-market trading parameters |

---

## ⚙️ Environment Variables

| Variable | Default | Description |
|---|---|---|
| `POSITION_REFRESH_SEC` | `5` | Position sync interval (seconds) |
| `MARKET_REFRESH_CYCLES` | `6` | Market metadata refresh (× position interval) |
| `STALE_TRADE_TTL_SEC` | `15.0` | Stale trade timeout (seconds) |
| `WS_RECONNECT_DELAY` | `1.0` | WebSocket reconnect delay (seconds) |
| `SHUTDOWN_TIMEOUT_SEC` | `10.0` | Graceful shutdown timeout |
| `LOG_LEVEL` | `INFO` | Logging level |
| `LOG_FILE` | `pms.log` | Log file path |

---

## 🤝 Support & Contact

<div align="center">

If this project helped you, consider buying me a coffee ☕

<a href="https://buymeacoffee.com/onlineunknowns">
  <img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-%23FFDD00?style=for-the-badge&logo=buymeacoffee&logoColor=black" height="40"/>
</a>

<br/><br/>

<a href="https://wa.me/201286016083">
  <img src="https://img.shields.io/badge/WhatsApp-25D366?style=for-the-badge&logo=whatsapp&logoColor=white" height="35"/>
</a>
&nbsp;
<a href="https://www.linkedin.com/in/onlineunknown/">
  <img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" height="35"/>
</a>

</div>

---

## 📄 License

MIT — see [LICENSE](LICENSE) for details.

<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=100&section=footer" width="100%"/>
</div>
