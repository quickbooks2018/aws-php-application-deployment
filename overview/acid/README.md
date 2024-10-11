# ACID in Relational Databases

## Introduction

In the context of relational databases, **ACID** is an acronym that stands for **Atomicity**, **Consistency**, **Isolation**, and **Durability**. These four properties ensure reliable processing of database transactions, maintaining data integrity and reliability even in cases of errors, crashes, or other unforeseen issues. ACID properties are critical for maintaining data integrity, especially in high-concurrency environments.

## Table of Contents

- [Atomicity](#atomicity)
- [Consistency](#consistency)
- [Isolation](#isolation)
- [Durability](#durability)
- [Examples](#examples)
- [References](#references)

## Atomicity

**Definition**: Atomicity ensures that each transaction is treated as a single "unit" of work. Either all operations within a transaction are completed successfully, or none of them are applied.

### Key Points
- If a transaction fails at any point, the database remains unchanged.
- No intermediate state of the transaction is visible outside of the transaction itself.

### Example
```sql
BEGIN TRANSACTION;
  UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
  UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;
COMMIT;
```
If the second `UPDATE` statement fails, the first `UPDATE` will also be rolled back, leaving the balances unchanged.

## Consistency

**Definition**: Consistency ensures that any transaction will bring the database from one valid state to another, adhering to all defined rules, constraints, and triggers.

### Key Points
- The database must remain consistent before and after the transaction.
- This property guarantees that all data written to the database must be valid according to all defined rules.

### Example
```sql
-- Assuming a constraint that account balance cannot go below 0
UPDATE accounts SET balance = balance - 1000 WHERE account_id = 1;
```
If `account_id 1` has a balance of 500, this transaction will be aborted to maintain consistency.

## Isolation

**Definition**: Isolation ensures that the operations of one transaction are isolated from other concurrent transactions. This prevents transactions from interfering with each other and guarantees that intermediate results are not visible to other transactions.

### Key Points
- The degree of isolation can vary depending on the isolation level (e.g., READ COMMITTED, REPEATABLE READ, SERIALIZABLE).
- Isolation prevents issues like dirty reads, non-repeatable reads, and phantom reads.

### Example
If two transactions are trying to update the same row simultaneously, isolation ensures that one transaction's updates are not visible to the other until it is complete.

## Durability

**Definition**: Durability guarantees that once a transaction has been committed, it will remain committed, even in the event of a system crash or power loss.

### Key Points
- Ensures that committed data is permanently stored.
- Data recovery mechanisms are used to restore committed transactions in case of hardware or software failure.

### Example
Once a `COMMIT` statement is executed, even if the server crashes immediately after, the committed data remains intact when the server is restarted.

## Examples

### Example 1: Banking Transaction
Consider a transaction where $500 is transferred from Account A to Account B:

```sql
BEGIN TRANSACTION;
  UPDATE accounts SET balance = balance - 500 WHERE account_id = 'A';
  UPDATE accounts SET balance = balance + 500 WHERE account_id = 'B';
COMMIT;
```
- **Atomicity**: If any of the updates fail, the entire transaction is rolled back.
- **Consistency**: The sum of balances remains unchanged.
- **Isolation**: No other transaction can access intermediate states.
- **Durability**: Once committed, the changes are permanent.

## References

- [Database System Concepts, 6th Edition](https://www.db-book.com/)
- [ACID Properties in DBMS](https://en.wikipedia.org/wiki/ACID)