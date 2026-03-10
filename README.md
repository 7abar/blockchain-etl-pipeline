# ⛓️ Blockchain ETL Pipeline

> Production-grade data pipeline that extracts on-chain data from Ethereum & Solana, transforms it into analytics-ready models, and loads it into BigQuery — powering DeFi dashboards and ML feature stores.

![Python](https://img.shields.io/badge/Python-3.11+-blue?logo=python)
![Airflow](https://img.shields.io/badge/Airflow-2.7-017CEE?logo=apacheairflow)
![dbt](https://img.shields.io/badge/dbt-1.7-FF694B?logo=dbt)
![BigQuery](https://img.shields.io/badge/BigQuery-ready-4285F4?logo=googlebigquery)
![Web3](https://img.shields.io/badge/Web3.py-6.0-F16822)

---

## 📌 Overview

A modular **ELT pipeline** that ingests raw blockchain data and transforms it into clean, queryable analytics models for DeFi research and ML feature engineering.

**What it does:**
- 🔗 Extracts blocks, transactions, token transfers, and DeFi events from **Ethereum** (via Web3.py + Alchemy) and **Solana** (via Helius API)
- ⚙️ Transforms raw data into normalized fact/dimension models using **dbt**
- 📦 Loads into **BigQuery** (or PostgreSQL for local dev)
- 🕐 Orchestrated with **Apache Airflow** — runs every 15 minutes

> ⚡ Processes ~50,000 transactions/hour | Supports Ethereum, Solana, Arbitrum, Base

---

## 🏗️ Architecture

```
Ethereum (Alchemy RPC) ──┐
                          ├──► Extractor ──► Raw Layer (BQ) ──► dbt Transform ──► Analytics Layer
Solana (Helius API) ──────┘       │
                                  └──► Airflow Orchestration ──► Alerts & Monitoring
```

**Data Layers:**
| Layer | Description | Storage |
|-------|-------------|---------|
| `raw` | Unmodified on-chain data | BigQuery (partitioned) |
| `staging` | Cleaned, typed, deduplicated | BigQuery |
| `marts` | Aggregated analytics models | BigQuery |
| `features` | ML-ready feature tables | BigQuery / Parquet |

---

## 🚀 Features

- ⛏️ **Multi-chain extraction** — Ethereum, Solana, Arbitrum, Base
- 🔄 **Incremental loading** — only processes new blocks since last run
- 🧹 **dbt models** — staging, marts, and ML feature tables with full lineage
- 🕐 **Airflow DAGs** — scheduled ingestion with retry logic and alerting
- 📊 **DeFi-specific models** — DEX trades, liquidity events, wallet activity
- 🧪 **dbt tests** — uniqueness, not-null, referential integrity on all models
- 🐳 **Dockerized** — local dev with PostgreSQL, production-ready for BigQuery

---

## 📁 Project Structure

```
blockchain-etl-pipeline/
├── src/
│   ├── extractors/
│   │   ├── ethereum.py       # Web3.py block & tx extraction
│   │   ├── solana.py         # Helius API extraction
│   │   └── base.py           # Abstract base extractor
│   ├── transformers/
│   │   └── normalize.py      # Pre-dbt Python transforms
│   └── loaders/
│       ├── bigquery.py       # BigQuery loader
│       └── postgres.py       # Local PostgreSQL loader
├── dbt/
│   ├── models/
│   │   ├── staging/          # stg_ethereum_txns, stg_token_transfers
│   │   ├── marts/            # defi_trades, wallet_activity, token_flows
│   │   └── features/         # ml_wallet_features, ml_token_features
│   └── tests/                # dbt data quality tests
├── airflow/
│   └── dags/
│       ├── ethereum_ingest.py
│       └── solana_ingest.py
├── sql/                      # Ad-hoc analysis queries
├── tests/                    # Unit tests
├── docker-compose.yml
├── requirements.txt
└── README.md
```

---

## ⚙️ Setup & Installation

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/blockchain-etl-pipeline.git
cd blockchain-etl-pipeline

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure environment
cp .env.example .env
# Fill in: ALCHEMY_API_KEY, HELIUS_API_KEY, GOOGLE_APPLICATION_CREDENTIALS

# 4. Start local stack (Airflow + PostgreSQL)
docker-compose up -d

# 5. Run dbt models
cd dbt && dbt run && dbt test
```

---

## 📡 Sample Output — Wallet Activity Table

```sql
SELECT * FROM marts.wallet_activity
WHERE date = CURRENT_DATE AND chain = 'ethereum'
ORDER BY tx_count DESC LIMIT 5;
```

| wallet_address | chain | tx_count | total_volume_usd | unique_protocols | first_seen |
|---|---|---|---|---|---|
| 0xd8dA...6045 | ethereum | 142 | 1,240,500 | 8 | 2021-03-15 |
| 0xAb5...3c1 | ethereum | 98 | 876,200 | 5 | 2022-01-04 |

---

## 🗄️ dbt Models

| Model | Layer | Description |
|---|---|---|
| `stg_ethereum_transactions` | Staging | Cleaned Ethereum transactions |
| `stg_token_transfers` | Staging | ERC-20 transfer events |
| `defi_trades` | Marts | Uniswap/Curve swap events |
| `wallet_activity` | Marts | Daily wallet-level aggregations |
| `ml_wallet_features` | Features | 30+ ML features per wallet |

---

## 🗺️ Roadmap

- [ ] Add Base and zkSync Era chains
- [ ] Real-time streaming with Kafka
- [ ] Wallet labeling (CEX, DEX, whale classification)
- [ ] dbt Semantic Layer integration

---

## 📄 License

MIT License
