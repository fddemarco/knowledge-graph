---
base: "[[Reading List.base]]"
Category:
  - Learning Path
Author: Me
Status: Coming up next
---
## 🧭 Modern Data Architecture Learning Path

---

**Theme:** From warehouses to lakehouses, mesh, and real-time data platforms.

---

## 🌉 1. **"Fundamentals of Data Engineering" – Joe Reis & Matt Housley**

**Why first:**

- This book gives a **practical overview of modern data architectures** — including lakehouses, ELT vs ETL, orchestration, streaming, and cloud-native tooling.
- Helps shift your mindset from traditional BI to **modern pipelines, distributed systems**, and **data products**.

✅ **Sets the stage** for modern architecture: cloud, scale, orchestration, and flexibility.

---

## 🧱 2. **"Streaming Systems" – Tyler Akidau et al.**

**Why second:**

- Modern systems demand **real-time data processing** — batch-only thinking is outdated.
- Learn about event time, windowing, and architectures like **Lambda, Kappa**, and **stream processors** (Beam, Flink, Kafka Streams).

✅ You’ll **understand stream processing deeply**, a pillar of lakehouse and data mesh infrastructure.

---

## 🧪 3. **"Designing Data-Intensive Applications" – Martin Kleppmann**

**Why third:**

- A modern classic that explains **storage engines, consistency models, replication, compaction**, and **data modeling trade-offs**.
- Critical when evaluating **distributed data systems** like Delta Lake, Iceberg, and Kafka.

✅ Builds a strong **system-level understanding** for evaluating and building modern architectures.

---

## 🧊 4. **Lakehouse-Specific Resources (choose one or more)**

### 🔹 **Databricks’ Lakehouse Fundamentals Course**

Free course from Databricks, covering Delta Lake, Unity Catalog, and the lakehouse design pattern.

✅ A quick, **practical primer** if you’re using Databricks.

### 🔹 **Apache Iceberg or Delta Lake documentation + blog posts**

Study **table formats** and how they enable ACID transactions in lakehouses.

✅ Understand **storage layer mechanics** of modern lakehouses.

---

## 🕸️ 5. **"Data Mesh: Delivering Data-Driven Value at Scale" – Zhamak Dehghani**

**Why fifth:**

- This is *the* book on Data Mesh, explaining **domain-oriented decentralization**, **data as a product**, and **federated governance**.
- Important shift from *monolithic warehouses* to **organizationally scalable data architectures**.

✅ Learn the **socio-technical principles** of data mesh — more about **process + ownership** than technology.

---

## 📦 6. **"The Enterprise Big Data Lake" – Alex Gorelik**

**Why sixth:**

- Focuses on the **management side of data lakes**, like metadata, governance, lineage, and **self-service architecture**.
- Complements your learning with a more **operational and governance** lens.

✅ Prepares you for **governance, access control, and cataloging** in modern platforms.

---

## 🧭 7. **"The Modern Data Stack" (Online Articles & Reports)**

**Why seventh:**

- Learn how tools like **dbt, Airflow, Dagster, Fivetran, Snowflake, BigQuery, DuckDB**, etc., fit together.
- Read from resources like:
    - [locallyoptimistic.com](https://locallyoptimistic.com/)
    - [themodernstack.dev](https://themodernstack.dev/)
    - dbt blog
    - Andreessen Horowitz’s “Modern Data Infrastructure” essays

✅ Stay **current** with trends in ELT, reverse ETL, data contracts, observability, and more.

---

## 🧠 Bonus (Advanced/Optional)

- **"Metadata Management with Apache Atlas"** (or equivalents like Amundsen/OpenMetadata)
    - Understand lineage, discovery, and governance tooling.
- **"Cloud Data Management" – Google’s Data Engineering on GCP Specialization**
    - Great for hands-on learning in a modern cloud-native context (GCP, BigQuery, Dataflow).

---

## 🧩 Suggested Learning Order (Summary)

| Step | Focus Area | Resource |
| --- | --- | --- |
| 1️⃣ | Overview of modern architectures | *Fundamentals of Data Engineering* |
| 2️⃣ | Real-time and stream processing | *Streaming Systems* |
| 3️⃣ | System architecture + scalability | *Designing Data-Intensive Applications* |
| 4️⃣ | Lakehouse architecture | *Databricks courses / Iceberg / Delta* |
| 5️⃣ | Organizational/data product thinking | *Data Mesh* |
| 6️⃣ | Governance and metadata | *The Enterprise Big Data Lake* |
| 7️⃣ | Tools, trends, and orchestration | *Modern Data Stack articles* |

---

Let me know if you want this structured as a PDF or Notion-style checklist with progress tracking!