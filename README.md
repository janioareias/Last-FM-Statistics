# Last.fm Statistics Pipeline

This repository contains an end-to-end data pipeline designed using production-inspired data engineering patterns. The project ingests music listening (scrobbling) data directly from the **Last.fm REST API**, processes it using the **Big Data ecosystem (PySpark)** following **Medallion Architecture** principles, and serves curated datasets to an analytical **Power BI** dashboard.

<img width="1445" height="816" alt="image" src="https://github.com/user-attachments/assets/0c5aa861-032c-4eea-8144-61f6cfc4eea7" />

---

## Project Architecture

The pipeline was designed using a multi-layered (Medallion) architecture to ensure data governance, immutable history, and clear separation of responsibilities:

~~~mermaid
graph LR
    A[Last.fm REST API] --> B[(Bronze Layer <br> Raw JSON)]
    B --> C[(Silver Layer <br> Parquet/Snappy)]
    C --> D[(Gold Layer <br> Analytics Parquet)]
    D --> E[Power BI Dashboard]

    style A fill:#b30000,stroke:#333,stroke-width:1px,color:#fff
    style B fill:#cd7f32,stroke:#333,stroke-width:1px,color:#fff
    style C fill:#c0c0c0,stroke:#333,stroke-width:1px,color:#111
    style D fill:#ffd700,stroke:#333,stroke-width:1px,color:#111
    style E fill:#f2c811,stroke:#333,stroke-width:1px,color:#111
~~~

### Bronze Layer (Raw)

**Ingestion:** Python scripts using the `requests` library to consume Last.fm API endpoints.

**Storage:** Raw `.json` files stored locally (simulating a Data Lake) and organized into timestamped subdirectories, preserving the original extraction history without modifications.

### Silver Layer

**Processing:** **PySpark**

**Transformations:**
- Normalization of complex and deeply nested payloads using `F.explode()`.
- Strict schema enforcement and data type casting.
- Data cleansing and removal of unnecessary columns.

**Storage:** Compressed Parquet files using the Snappy codec, dramatically reducing storage consumption while providing high-performance columnar queries.

### Gold Layer

**Business Logic:** Advanced aggregations and Spark Window Functions are applied to generate analytics-ready datasets.

### Implemented Metrics

- **Loop Index (Fandom Loyalty):** Measures listening repetition by calculating the ratio between total plays and distinct tracks listened to for a given artist, highlighting highly repetitive listening patterns.

- **Album Dominance (Portfolio Share):** Percentage-based metric that quantifies how much a single album represents within the listener's total consumption of an artist's catalog.

- **Certification Engine:** Rule-based classification of tracks and artists into industry-style tiers (Gold, Platinum, and Diamond) according to stream volume thresholds.

### Data Visualization (Power BI)

The final layer consumes the consolidated Gold datasets, enabling multidimensional analysis through:

- **Engagement vs. Dominance Matrix:** Scatter plot crossing total stream volume with portfolio concentration, helping identify focused listening behavior versus diversified consumption patterns.

- **Advanced Rankings:** Dynamic rankings for artists, albums, and tracks enriched with certification badges and performance indicators.

- **Interactive Drill-Down Analysis:** Exploration of listening behavior across multiple dimensions, including artists, albums, tracks, and listening trends.

---

## Technical Highlights

- REST API Data Ingestion
- Medallion Architecture (Bronze, Silver, Gold)
- PySpark Data Processing
- Nested JSON Flattening
- Window Functions
- Columnar Storage with Parquet/Snappy
- Analytical Data Modeling
- Power BI Dashboarding

---

## How to Run the Pipeline

> [!IMPORTANT]
> You will need a Last.fm API account: https://www.last.fm/api/account/create

### Clone the Repository

~~~bash
git clone https://github.com/janioareias/Last-FM-Statistics.git
cd Last-FM-Statistics

pip install requests pyspark
~~~

### API Configuration

Insert your Last.fm API credentials and username into the global variables located in the first cells of the notebook.

### Execution

Run the cells in `last_fm_statistics.ipynb` sequentially to generate the `/bronze`, `/silver`, and `/gold` directories in your environment.
