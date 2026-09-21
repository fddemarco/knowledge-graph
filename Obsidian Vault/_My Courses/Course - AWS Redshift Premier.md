
## Service Introduction

**Amazon Redshift** is a fully managed, cloud-native data warehouse service designed for analytical data, complex queries, business intelligence, and machine learning. Data warehouses store and analyze aggregate data, while relational databases such as Amazon Aurora store and maintain individual records. Amazon Redshift automatically provisions infrastructure and manages administrative tasks such as backups, replication, and fault tolerance.

**Concurrency Scaling** automatically adds temporary cluster capacity when concurrent read queries increase and removes the additional capacity when demand decreases. It supports virtually unlimited concurrent users and queries.

**Redshift Spectrum** allows queries against data stored in Amazon S3 without first loading the data into Redshift. It allows queries to access data in both Redshift and Amazon S3 simultaneously, reducing the need to duplicate data and extending analytical capabilities beyond local warehouse storage.

Amazon Redshift uses a **massively parallel, columnar architecture** optimized for analytical queries. A Redshift cluster consists of a **single leader node and multiple compute nodes**. Clients send SQL queries to the leader node. The leader node breaks the query into jobs and distributes them to the compute nodes in parallel. Compute nodes contain the data, perform the required operations, and return results to the leader node. The leader node aggregates the results and returns the final result to the client.

Amazon Redshift can be used to build a unified data platform by querying data in Redshift and Amazon S3 without requiring all data to be loaded into the warehouse. Migrating an on-premises data warehouse to Amazon Redshift can improve query performance and reduce infrastructure costs.

Redshift cluster pricing is based on the selected node type and the number of nodes. Each node includes memory, storage, and I/O.

- **On-Demand pricing** has no upfront costs and charges an hourly rate based on node type and quantity.
- Each cluster receives up to one hour of free **Concurrency Scaling** credits per day. Usage beyond the free credits is charged at a per-second rate.
- **Reserved Instances** offer savings of up to 75% compared with On-Demand pricing in exchange for a one or three-year commitment.
- **Redshift Spectrum charges** based on the amount of data scanned in Amazon S3, in addition to cluster costs. Data transferred between Redshift and Amazon S3 within the same AWS Region is free for backup, restore, load, and unload operations. Other data transfers use standard AWS data transfer rates.

## Service Technical Overview

