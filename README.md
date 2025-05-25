# PySpark Function Cheat Sheet

Use this living document to record each PySpark function you learn. For every entry, fill out:
- **Purpose:** Why/when to use it  
- **Syntax:** How to call it  
- **Example:** A minimal code snippet  
- **Notes:** Edge cases or performance tips  

---

## 1. Core DataFrame & SparkSession

| Function                   | Purpose                                          | Syntax                                           | Example                                                           |
|----------------------------|--------------------------------------------------|--------------------------------------------------|-------------------------------------------------------------------|
| `SparkSession.builder`     | Initialize a SparkSession                        | `SparkSession.builder.appName(name).getOrCreate()` | ```python<br>spark = SparkSession.builder.appName("app").getOrCreate()<br>``` |
| `df.explain([mode])`       | Show Spark’s query plan (logical & physical)     | `df.explain()`<br>`df.explain("formatted")`       | Prints parsed/optimized logical plans and the physical plan. Use to spot costly shuffles (`Exchange`) or confirm broadcast joins (`BroadcastExchange`). |
| `df.cache()`               | Persist the DataFrame in memory                  | `df_cached = df.cache()`                         | ```python<br>df2 = df.filter("qty > 0")<br>df2_cached = df2.cache()<br>df2_cached.count()  # materializes cache<br>``` |
| `df.persist([storageLevel])` | Persist with a custom storage level (MEMORY_AND_DISK, etc.) | `df_p = df.persist(StorageLevel.MEMORY_AND_DISK)` | ```python<br>from pyspark import StorageLevel<br>df_p = df.persist(StorageLevel.MEMORY_AND_DISK)<br>df_p.count()<br>``` |
| `df.unpersist([blocking])` | Remove the DataFrame from all caches             | `df.unpersist(blocking=True)`                    | ```python<br>df2_cached.unpersist(blocking=True)<br>```           |

---

## 2. Reading & Writing

| Function               | Purpose                     | Syntax                                                        | Example                                                                   |
|------------------------|-----------------------------|---------------------------------------------------------------|---------------------------------------------------------------------------|
| `spark.read.csv()`     | Read a CSV file             | `spark.read.csv(path, header=True, inferSchema=True)`         | ```python<br>df = spark.read.csv("data.csv", header=True, inferSchema=True)<br>``` |
| `.option()`            | Specify read/write options  | `.read.option("key", value)` or `.write.option("key", value)` | ```python<br>df = spark.read.option("header", True).csv("data.csv")<br>``` |
| `.mode()`              | Write mode (overwrite, append) | `.write.mode("overwrite")`                                   | ```python<br>df.write.mode("overwrite").csv("out.csv")<br>```             |
| `df.write.parquet()`   | Write Parquet file          | `df.write.parquet(path)`                                      | ```python<br>df.write.parquet("out.parquet")<br>```                       |
| `.partitionBy()`       | Partition output by columns | `.write.partitionBy("col")`                                   | ```python<br>df.write.partitionBy("Country").parquet("out/")<br>```       |

---

## 3. Column Selection & Projection

| Function               | Purpose                       | Syntax                                     | Example                                                                  |
|------------------------|-------------------------------|--------------------------------------------|--------------------------------------------------------------------------|
| `select()`             | Choose columns                | `df.select("col1", "col2")`                | ```python<br>df.select("InvoiceNo", "Quantity")<br>```                   |
| `col()`                | Reference a column            | `col("col")`                               | ```python<br>from pyspark.sql.functions import col<br>df.select(col("col"))<br>``` |
| `withColumn()`         | Create or replace a column    | `df.withColumn("new", expr)`               | ```python<br>df.withColumn("Total", col("Qty")*col("Price"))<br>```      |
| `drop()`               | Remove columns                | `df.drop("col")`                           | ```python<br>df.drop("Description")<br>```                               |
| `alias()`              | Rename a column in expression | `col("col").alias("new")`                  | ```python<br>df.select(col("Qty").alias("Quantity"))<br>```              |
| `cast()`               | Change column type            | `col("col").cast("type")`                  | ```python<br>df.withColumn("Date", col("Date").cast("date"))<br>```     |

---

## 4. Filtering & Conditional

| Function                   | Purpose                                                       | Syntax                                            | Example                                                                 |
|----------------------------|---------------------------------------------------------------|---------------------------------------------------|-------------------------------------------------------------------------|
| `filter()` / `where()`     | Keep rows matching condition                                  | `df.filter("col > 0")` or `df.where(expr)`        | ```python<br>df.filter(col("Quantity")>0)<br>```                        |
| `when()` / `otherwise()`   | Conditional expressions                                        | `when(cond, true).otherwise(false)`               | ```python<br>df.withColumn("Flag", when(col("Qty")>0,"OK").otherwise("X"))<br>``` |
| `isNull()` / `isnan()`     | Detect null / NaN                                             | `col("col").isNull()` or `col("col").isnan()`     | ```python<br>df.filter(col("CustID").isNull())<br>```                   |
| `coalesce()`               | Replace null with first non-null                               | `coalesce(col1, col2)`                            | ```python<br>df.withColumn("Cust", coalesce(col("CustID"), lit(-1)))<br>``` |
| `nullif()`                 | Turn matching value into null                                 | `expr("nullif(col,'')")`                          | ```python<br>df.withColumn("Desc", expr("nullif(Description,'')"))<br>``` |

---

## 5. String Functions

| Function                  | Purpose                                    | Syntax                               | Example                                                   |
|---------------------------|--------------------------------------------|--------------------------------------|-----------------------------------------------------------|
| `lower()` / `upper()`     | Change case                                | `lower(col("col"))`                  | ```python<br>df.withColumn("lower", lower("Description"))<br>``` |
| `trim()` / `ltrim()`      | Remove whitespace                          | `trim(col("col"))`                   | ```python<br>df.withColumn("DescTrim", trim("Description"))<br>``` |
| `concat()` / `concat_ws()` | Concatenate columns                        | `concat(col("a"),col("b"))`          | ```python<br>df.select(concat_ws("-", "Country","InvoiceNo"))<br>``` |
| `substring()`             | Extract substring                          | `substring("col", pos, len)`         | ```python<br>df.withColumn("first10", substring("Description",1,10))<br>``` |
| `split()`                 | Split string into array                     | `split(col("col"), "\\s+")`          | ```python<br>df.withColumn("words", split("Description","\\s+"))<br>``` |
| `regexp_replace()`        | Replace via regex                          | `regexp_replace("col", "pattern","repl")` | ```python<br>df.withColumn("noDigits", regexp_replace("Description","\\d+",""))<br>``` |

---

## 6. Numeric Functions

| Function                   | Purpose                                  | Syntax                         | Example                                                    |
|----------------------------|------------------------------------------|--------------------------------|------------------------------------------------------------|
| `abs()`                    | Absolute value                           | `abs(col("col"))`              | ```python<br>df.withColumn("absQty", abs("Quantity"))<br>``` |
| `round()`                  | Round to N decimals                      | `round(col("col"), 2)`         | ```python<br>df.withColumn("rndPrice", round("UnitPrice",2))<br>``` |
| `ceil()` / `floor()`       | Round up / down                          | `ceil("col")` / `floor("col")` | ```python<br>df.withColumn("ceilQty", ceil("Quantity"))<br>``` |
| `sqrt()`, `exp()`, `log()` | Math functions                            | `sqrt("col")`, `log("col")`    | ```python<br>df.withColumn("logPrice", log("UnitPrice"))<br>``` |

---

## 7. Date & Timestamp

| Function                   | Purpose                                        | Syntax                               | Example                                                          |
|----------------------------|------------------------------------------------|--------------------------------------|------------------------------------------------------------------|
| `to_date()` / `to_timestamp()` | Parse strings to date/timestamp              | `to_date("col","format")`            | ```python<br>df.withColumn("Date", to_date("InvoiceDate","MM/dd/yyyy"))<br>``` |
| `year()`, `month()`, `dayofmonth()` | Extract parts                            | `year("Date")`                       | ```python<br>df.withColumn("yr", year("Date"))<br>```             |
| `date_add()` / `date_sub()`      | Add / subtract days                        | `date_add("Date",7)`                 | ```python<br>df.withColumn("plus7", date_add("Date",7))<br>```    |
| `datediff()` / `months_between()` | Difference in days / months                 | `datediff(l, r)`                     | ```python<br>df.withColumn("days", datediff(current_date(),"Date"))<br>``` |
| `current_date()` / `current_timestamp()` | Current date / timestamp                | `current_date()`                     | ```python<br>df.withColumn("now", current_timestamp())<br>```     |

---

## 8. Complex Types (Array, Struct, Map)

| Function                   | Purpose                                   | Syntax                            | Example                                                             |
|----------------------------|-------------------------------------------|-----------------------------------|---------------------------------------------------------------------|
| `array()` / `struct()`     | Create array / struct                     | `array("a","b")`                  | ```python<br>df.withColumn("arr", array("StockCode","Quantity"))<br>``` |
| `explode()` / `posexplode()` | Flatten arrays                            | `explode("arr")`                  | ```python<br>df.select("InvoiceNo", explode("arr"))<br>```           |
| `size()` / `array_contains()` | Array utilities                          | `size("arr")`, `array_contains("arr",val)` | ```python<br>df.withColumn("len", size("arr"))<br>```               |
| `map_keys()` / `map_values()` | Map utilities                            | `map_keys("map")`                 | ```python<br>df.withColumn("keys", map_keys("map"))<br>```           |

---

## 9. Higher-Order Functions

| Function                   | Purpose                                       | Syntax (example)                               | Example                                                                                          |
|----------------------------|-----------------------------------------------|------------------------------------------------|--------------------------------------------------------------------------------------------------|
| `transform()`              | Apply function to each element                | `expr("transform(arr, x -> x+1)")`             | ```python<br>df.withColumn("inc", expr("transform(arr, x->x+1)"))<br>```                         |
| `filter()` (HOF)           | Keep elements matching predicate               | `expr("filter(arr, x -> x>3)")`                | ```python<br>df.withColumn("gt3", expr("filter(arr, x->x>3)"))<br>```                            |
| `aggregate()`              | Reduce array to single value                   | `expr("aggregate(arr, 0, (acc,x)->acc+x)")`    | ```python<br>df.withColumn("sum", expr("aggregate(arr,0,(a,x)->a+x)"))<br>```                     |
| `exists()`                 | Check if any element matches                  | `expr("exists(arr, x -> x=5)")`                | ```python<br>df.withColumn("has5", expr("exists(arr,x->x=5)"))<br>```                             |
| `zip_with()`               | Combine two arrays elementwise                | `expr("zip_with(a,b,(x,y)->x*y)")`             | ```python<br>df.withColumn("prod", expr("zip_with(a,b,(x,y)->x*y)"))<br>```                       |

---

## 10. Aggregations & Grouping

| Function                          | Purpose                                 | Syntax                                    | Example                                                                        |
|-----------------------------------|-----------------------------------------|-------------------------------------------|--------------------------------------------------------------------------------|
| `groupBy()`                       | Group rows by columns                   | `df.groupBy("col")`                       | ```python<br>df.groupBy("Country").sum("TotalPrice")<br>```                     |
| `agg()`                           | Multiple aggregations                   | `agg(sum("x"), avg("y"))`                 | ```python<br>df.groupBy("Country").agg(sum("Qty"), avg("Price"))<br>```         |
| `countDistinct()` / `approx_count_distinct()` | Unique counts               | `countDistinct("col")`                    | ```python<br>df.groupBy("Country").agg(countDistinct("CustID"))<br>```          |
| `pivot()`                         | Pivot a column into multiple columns    | `pivot("col", ["A","B"])`                 | ```python<br>df.groupBy("Year").pivot("Country",["UK","FR"]).sum("Sales")<br>``` |

---

## 11. Joins & Broadcast

| Function                 | Purpose                         | Syntax                                      | Example                                                                 |
|--------------------------|---------------------------------|---------------------------------------------|-------------------------------------------------------------------------|
| `join()`                 | Join two DataFrames             | `df.join(df2, on="key", how="inner")`       | ```python<br>df.join(df2, "key")<br>```                                  |
| `broadcast()`            | Broadcast small DataFrame       | `broadcast(df_small)`                       | ```python<br>from pyspark.sql.functions import broadcast<br>df.join(broadcast(df2),"key")<br>``` |

---

## 12. Window Functions

| Function                  | Purpose                                 | Syntax                                           | Example                                                       |
|---------------------------|-----------------------------------------|--------------------------------------------------|---------------------------------------------------------------|
| `Window.partitionBy()`    | Define window partition                | `Window.partitionBy("col").orderBy("col")`        | ```python<br>from pyspark.sql.window import Window<br>win=Window.partitionBy("Cust").orderBy("Date")<br>``` |
| `row_number()`, `lag()`, `lead()` | Ranking & offsets              | `row_number().over(win)`, `lag("col",1).over(win)` | ```python<br>df.withColumn("rn", row_number().over(win))<br>``` |

---

## 13. Partitioning & Performance

| Function                 | Purpose                                      | Syntax                             | Example                                                                 |
|--------------------------|----------------------------------------------|------------------------------------|-------------------------------------------------------------------------|
| `repartition()`          | Increase or change partitions                | `df.repartition(n, "col")`         | ```python<br>df.repartition(10,"Country")<br>```                        |
| `coalesce()`             | Reduce number of partitions                  | `df.coalesce(n)`                   | ```python<br>df.coalesce(2)<br>```                                       |
| `sample()` / `sampleBy()`| Random sampling                               | `df.sample(fraction=0.1)`          | ```python<br>df.sample(0.1, seed=42)<br>```                             |

---

## 14. UDF & SQL API

| Function                       | Purpose                                           | Syntax                                          | Example                                                                           |
|--------------------------------|---------------------------------------------------|-------------------------------------------------|-----------------------------------------------------------------------------------|
| `udf()` / `pandas_udf()`       | Define scalar/vectorized UDF                      | `udf(func, returnType)`                         | ```python<br>@udf("string")<br>def classify(q):<br>    return "big" if q>10 else "small"<br>df.withColumn("cls", classify("Quantity"))<br>``` |
| `createOrReplaceTempView()` / `spark.sql()` | Run SQL queries                              | `df.createOrReplaceTempView("t"); spark.sql(query)` | ```python<br>df.createOrReplaceTempView("retail")<br>spark.sql("SELECT Country, SUM(TotalPrice) FROM retail GROUP BY Country")<br>``` |

---

## 15. Utility & Metadata

| Function               | Purpose                               | Syntax                                     | Example                                                      |
|------------------------|---------------------------------------|--------------------------------------------|--------------------------------------------------------------|
| `df.columns` / `df.dtypes` | List column names and types        | `df.columns`, `df.dtypes`                  | ```python<br>print(df.columns, df.dtypes)<br>```             |
| `df.schema`            | Print full schema                     | `df.schema`                                | ```python<br>print(df.schema)<br>```                         |
| `df.na.drop()` / `df.na.fill()` | Handle missing values          | `df.na.drop()`, `df.na.fill({})`           | ```python<br>df.na.fill({"Description":"UNK"})<br>```       |
| `df.toPandas()`        | Convert to Pandas DataFrame (small data) | `df.toPandas()`                            | ```python<br>pdf = df.limit(1000).toPandas()<br>```          |
