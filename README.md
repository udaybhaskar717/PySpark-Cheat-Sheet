# PySpark Function Cheat Sheet

Use this living document to record each PySpark function you learn. For every entry, fill out:
- **Purpose:** Why/when to use it  
- **Syntax:** How to call it  
- **Example:** A minimal code snippet  
- **Notes:** Edge cases or performance tips  

---

## 1. Core DataFrame & SparkSession

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
| `SparkSession.builder`   | Initialize SparkSession | `SparkSession.builder.appName(name).getOrCreate()`   | `spark = SparkSession.builder.appName("app").getOrCreate()`      |
| `df.explain([mode])`      | Show Spark's query plan (Logical and physical)| `df.explain()`,`df.explain("formatted")`| Prints Parsed/Analyzed/Optimized logical plans and the Physical plan. Use it to spot expensive shuffles (`Exchange`) or confirm broadcast joins (`BroadcastExchange`) for performance tuning. |
| `df.cache(()` | To the intermediate results in RAM, it avoid re computations | 




**What is Spark**

Think of spark like a restaurant kitchen:
1. You (the chef) write down a recipe (your DataFrame code):
  'Take the raw ingredients (Data), remove the bad bits (filters), then plate only the fields I care about (selected columns)'
2. Sprk doesn't immediately start cooking, insted, it writes you a *step-by-step plan* in the back kitchen (the "query plan"), figures out which tasks can be done in parallel, and only then sends the orders to the cooks (executors) 

how to use df.explain

* Purpose: Reveal exactly how spark will execute your DatFrame pipeline
* syntax variant:
   * df.explain() - physical plan only
   * df.explain(True) - + parsed/optimized logial plans
   * df.explain("formatted") - nicely indented physical plan
**key notes**
  * Look for 'Exchange hashpartitioning' -> constly shuffle
  * Lokk for 'BroadcastExchange' -> good for small-table joins
  * Wrap in AQE? you may see 'AdaptiveSparkPlan' at the top
---


| **Plan**     | After caching, a second call will show an `InMemoryTableScan` instead of repeating scans and filters. |

---

## 3. `df.persist([storageLevel])`

| Aspect       | Details                                                                          |
|--------------|----------------------------------------------------------------------------------|
| **What**     | Like `.cache()`, but you choose **where** to store the data (memory, disk, etc.)|
| **Options**  | Common `StorageLevel` values:<br/>• `MEMORY_ONLY` (default)<br/>• `MEMORY_AND_DISK`<br/>• `DISK_ONLY`<br/>• `MEMORY_ONLY_SER` (serialized)<br/>• plus replication variants   |
| **Why**      | - Data too big for RAM?<br/>- Want spill-to-disk if memory fills?<br/>- Need persistence across node failures? |
| **Example**  | ```python
from pyspark import StorageLevel

# Persist in memory, spill to disk when needed
df2 = df.filter("Quantity > 0").withColumn("Total", ...)
df2_persisted = df2.persist(StorageLevel.MEMORY_AND_DISK)

# Materialize
  df2_persisted.count()
  | **Plan**     | Similar to `cache()`, subsequent operations read from an **in-memory or on-disk** table scan. |
  
  
  ## 4. `df.unpersist([blocking])`
  
  | Aspect       | Details                                                                          |
  |--------------|----------------------------------------------------------------------------------|
  | **What**     | Tells Spark “I’m done with this cached/persisted DataFrame—free the storage.”   |
  | **blocking** | - `True` (default): wait until data is actually removed before moving on.<br/>- `False`: uncache request is asynchronous. |
  | **Why**      | - Free up RAM/disk for other jobs.<br/>- Clean up long-running applications. |
  | **Example**  | ```python
  # After you finish all operations on df2_cached:
  df2_cached.unpersist(blocking=True)

  | **Plan**     | Later `explain()` calls will no longer show an `InMemoryTableScan` for that DataFrame. |
    
  ### 🔑 Key Takeaways
  
    1. **When to cache/persist:**  
       - You use the **same** DataFrame in multiple actions (e.g., counts, writes, joins).  
       - You have an **expensive** pipeline you don’t want re-running.  
    
    2. **Choosing storage level:**  
       - Start with `cache()` (memory only).  
       - If you run out of RAM, switch to `persist(MEMORY_AND_DISK)`.  
    
    3. **Always unpersist:**  
       - In long workflows or notebooks, call `.unpersist()` when you no longer need the cached data.  
    
    4. **Verify with `explain()`:**  
       - After caching, rerun `df.explain("formatted")` on a **cached** DataFrame and look for `InMemoryTableScan`—that confirms Spark is reading from cache.  
  
  By fitting these into your day-to-day code and watching how your execution plans change, you’ll quickly gain muscle-memory on when and how to cache for maximum speed.

```
## 2. Reading & Writing

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 3. Column Selection & Projection

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 4. Filtering & Conditional

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 5. String Functions

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 6. Numeric Functions

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 7. Date & Timestamp

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 8. Complex Types (Array, Struct, Map)

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 9. Higher-Order Functions

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 10. Aggregations & Grouping

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 11. Joins & Broadcast

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 12. Window Functions

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 13. Partitioning & Performance

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 14. UDF & SQL API

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |

---

## 15. Utility & Metadata

| Function                 | Purpose               | Syntax                                               | Example                                                            |
|--------------------------|-----------------------|------------------------------------------------------|--------------------------------------------------------------------|
|                          |                       |                                                      |                                                                    |
