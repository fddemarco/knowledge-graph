[[Data Modeling]]
[[Book - The Data Warehouse Toolkit]] - Chapter 8 - CRM

## Accumulating snapshot

Accumulating snapshot fact tables depend on a series of dates that implement the “standard scenario” for the pipeline process. For order fulfillment, you may have the steps of order created, order shipped, order delivered, order paid, and order returned as standard steps in the order scenario. This kind of design is successful when 90 percent or more of the orders progress through these steps (hopefully without the return) without any unusual exceptions.

But if an occasional situation deviates from the standard scenario, you don’t have a good way to reveal what happened. For example, maybe when the order was shipped, the delivery truck had a fl at tire. A decision was made to unload the delivery to another truck, but unfortunately it began to rain and the shipment was water damaged. Then it was refused by the customer, and ultimately there was a lawsuit. None of these unusual steps are modeled in the standard scenario in the accumulating snapshot. Nor should they be!

The way to describe unusual departures from the standard scenario is to add a delivery status dimension to the accumulating snapshot fact table. For the case of the weird delivery scenario, you tag this order fulfillment row with the status Weird. Then if the analyst wants to see the complete story, the analyst can join to a companion transaction fact table through the order number and line number that has every step of the story. The transaction fact table joins to a transaction dimension, which indeed has Flat Tire, Damaged Shipment, and Lawsuit as transactions. Even though this transaction dimension will grow over time with unusual entries, it is well bounded and stable.
## Timespan

A **timespan fact table** stores a transaction/event together with:

1. **The timestamp when the transaction occurred**
2. **The timestamp when the next transaction occurred**

This turns each transaction into a **time interval**.

```text
transaction_time              next_transaction_time
       ↓                               ↓
       |-------------------------------|
              customer's state
```

The key idea is that each row represents the customer's state from its transaction timestamp until the next transaction timestamp. Suppose a customer has these events:

|Event|Transaction Time|Next Transaction|State|
|---|---|---|---|
|Fraud alert started|Jan 10|Jan 20|Fraud alert|
|Fraud alert ended|Jan 20|Feb 5|Normal|
|Fraud alert started|Feb 5|Feb 12|Fraud alert|
|Fraud alert ended|Feb 12|Mar 1|Normal|

The rows now represent intervals:

```text
Jan 10 ───────── Jan 20 ───────── Feb 5 ─────── Feb 12 ───────── Mar 1
   |                 |                |              |
   └─ FRAUD ─────────┘                └─ FRAUD ──────┘
                     └──── NORMAL ────┘
```

The first row effectively means that the customer was on **fraud alert from Jan 10 until Jan 20**. Without `next_transaction_time`, you only know **when something happened**. With it, you know **how long that state remained valid**. The important trick is that you often **don't know the end time when the transaction happens**.

Timespan fact tables transform a sequence of transactions into continuous time intervals, making it possible to efficiently reconstruct the state of something at any arbitrary point in the past.

Using the pair of date/time stamps requires a two-step process whenever a new transaction row is entered. In the first step, the end effective date/time stamp of the most current transaction must be set to a fictitious date/time far in the future. Although it would be semantically correct to insert NULL for this date/time, nulls become a headache when you encounter them in constraints because they can cause a database error when you ask if the fi eld is equal to a specific value. By using a fictitious date/time far in the future, this problem is avoided.

In the second step, after the new transaction is entered into the database, the ETL process must retrieve the previous transaction and set its end effective date/time to the date/time of the newly entered transaction. Although this two-step process is a noticeable cost of this twin date/time approach, it is a classic and desirable trade-off between extra ETL overhead in the back room and reduced query complexity in the front room.
