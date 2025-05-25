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
| 'df.explain([mode])      | Show Spark's query plan (Logical and physical)| 'df.explain()','df.explain("formatted")'| Prints Parsed/Analyzed/Optimized logical plans and the Physical plan. Use it to spot expensive shuffles (`Exchange`) or confirm broadcast joins (`BroadcastExchange`) for performance tuning. |




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
