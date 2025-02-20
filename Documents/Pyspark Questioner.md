# **PySpark Interview Questions and Answers**

## **Basic: Laying the Foundation**

### **1. What is PySpark, and how does it differ from Apache Spark?**
**PySpark** is the Python API for **Apache Spark**, allowing Python developers to leverage Spark's distributed computing capabilities. The key difference is that Apache Spark is implemented in **Scala** and **Java**, while PySpark provides a **Pythonic** interface.

### **2. What are the key components of PySpark?**
- **SparkSession**: The entry point for using Spark functionality.
- **RDD (Resilient Distributed Dataset)**: Low-level immutable data structure.
- **DataFrame**: Higher-level API that provides SQL-like operations on structured data.
- **Dataset**: Available in Scala/Java but not in PySpark.
- **Transformations and Actions**: Operations performed on RDDs/DataFrames.
- **PySpark SQL**: Module for processing structured data.
- **MLlib**: Machine learning library.
- **GraphX**: Library for graph processing.

### **3. How do you create a SparkSession in PySpark?**
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("MyApp").getOrCreate()
```

### **4. What is an RDD, how does it differ from a DataFrame?**
- **RDD (Resilient Distributed Dataset)**: Immutable, low-level distributed collection of objects.
- **DataFrame**: Higher-level abstraction of structured data with schema, optimized for performance.
- **Difference**: DataFrames are more optimized due to **Catalyst Optimizer** and **Tungsten Execution Engine**.

### **5. How do you create an RDD in PySpark?**
```python
rdd = spark.sparkContext.parallelize([("Alice", 29), ("Bob", 35)])
```

### **6. What are transformations and actions in PySpark?**
- **Transformations**: Lazy operations that return a new RDD (e.g., `map()`, `filter()`, `groupBy()`).
- **Actions**: Triggers execution and returns values (e.g., `collect()`, `count()`, `show()`).

### **7. How do you read a CSV file into a DataFrame in PySpark?**
```python
df = spark.read.csv("data.csv", header=True, inferSchema=True)
```

### **8. Explain the difference between narrow & wide transformations.**
- **Narrow Transformations**: Only require data from a **single partition** (e.g., `map()`, `filter()`).
- **Wide Transformations**: Require **shuffling** across partitions (e.g., `groupBy()`, `join()`).

### **9. How does PySpark handle lazy evaluation?**
- PySpark **delays execution** until an **action** is called.
- This improves **performance** by optimizing the DAG (Directed Acyclic Graph) of computations.

### **10. What is the difference between `select()`, `withColumn()`, and `selectExpr()`?**
- **`select()`**: Extracts specific columns.
- **`withColumn()`**: Modifies an existing column or adds a new one.
- **`selectExpr()`**: Allows SQL expressions.

```python
df.select("name").show()  
df.withColumn("new_col", df["age"] + 10).show()
df.selectExpr("name", "age + 5 as new_age").show()
```

### **11. How do you filter rows in a PySpark DataFrame?**
```python
df.filter(df.age > 30).show()
df.where("age > 30").show()
```

### **12. How do you handle missing values in PySpark?**
```python
df.fillna(0).show()  
df.dropna().show()
```

### **13. How do you add a new column to an existing DataFrame?**
```python
df = df.withColumn("new_col", df["age"] * 2)
```

### **14. What are PySpark’s built-in functions, and how do you use them?**
PySpark provides functions under `pyspark.sql.functions`:
```python
from pyspark.sql.functions import col, lit, upper
df.withColumn("uppercase_name", upper(col("name"))).show()
```

### **15. How do you convert a PySpark DataFrame to Pandas?**
```python
pandas_df = df.toPandas()
```

### **16. How do you perform `groupBy` and aggregation in PySpark?**
```python
df.groupBy("category").sum("sales").show()
```

### **17. What is the difference between `orderBy()` & `sort()` in PySpark?**
- **`orderBy()`**: Supports both ascending and descending.
- **`sort()`**: Similar but works only on DataFrames.

```python
df.orderBy("age", ascending=False).show()
```

### **18. How do you drop duplicate rows from a PySpark DataFrame?**
```python
df.dropDuplicates().show()
```

### **19. How do you write a PySpark DataFrame to a CSV file?**
```python
df.write.csv("output.csv", header=True)
```

## **Intermediate and Advanced Topics**

### **1. How do you implement structured streaming in PySpark?**
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import *

spark = SparkSession.builder.appName("StructuredStreaming").getOrCreate()

df = spark.readStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "topic_name") \
    .load()

query = df.selectExpr("CAST(value AS STRING)").writeStream \
    .outputMode("append").format("console").start()

query.awaitTermination()
```

### **2. How do you integrate PySpark with Kafka for real-time processing?**
#### **Read from Kafka**
```python
df = spark.readStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "my_topic") \
    .load()
```
#### **Write to Kafka**
```python
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)") \
    .writeStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("topic", "output_topic") \
    .start()
```

# **Intermediate PySpark Interview Questions and Answers**

## **1. What is the difference between DataFrame & Dataset in PySpark?**
- **DataFrame**: Schema-based, distributed collection of structured data.
- **Dataset**: Strongly typed API available only in Scala and Java.
- **Difference**: PySpark does not support Datasets, but in Scala/Java, they offer type safety.

## **2. Explain how PySpark handles schema inference when reading data.**
- Uses the `inferSchema=True` option to deduce data types from files.
- Example:
```python
df = spark.read.csv("data.csv", header=True, inferSchema=True)
```

## **3. How do you optimize PySpark performance?**
### **Techniques for Optimization:**
- **Use Columnar Format (Parquet) instead of CSV**
- **Reduce Shuffling** with `coalesce()`
- **Cache/Persist Data** for frequent reuse
- **Use Broadcast Joins** to optimize small tables
- **Optimize Partitions**

## **4. What is the role of partitions in PySpark?**
- Distributes data across nodes for parallel processing.
- Helps optimize performance by avoiding data skew.
- Example:
```python
df.repartition(4)
```

## **5. How do you manually repartition a DataFrame?**
```python
df = df.repartition(10)
```
- **`repartition(n)`**: Increases or decreases partitions (full shuffle).
- **`coalesce(n)`**: Reduces partitions **without full shuffle**.

## **6. What are PySpark’s window functions, and how do you use them?**
Window functions perform calculations across a group of rows.
```python
from pyspark.sql.window import Window
from pyspark.sql.functions import rank

window_spec = Window.partitionBy("category").orderBy("sales")
df.withColumn("rank", rank().over(window_spec)).show()
```

## **7. Explain the difference between `coalesce()` & `repartition()`**
- **`repartition(n)`**: Increases or decreases partitions (causes full shuffle).
- **`coalesce(n)`**: Reduces partitions **without full shuffle** (more efficient).

## **8. How do you join two PySpark DataFrames?**
```python
df1.join(df2, "id", "inner").show()
```

## **9. What are different types of joins in PySpark?**
- **Inner Join**
- **Left/Right Outer Join**
- **Full Outer Join**
- **Cross Join**
- **Self Join**

## **10. How do you handle skewed data in PySpark?**
### **Techniques:**
- **Salting Technique**: Add random values to keys to distribute load.
- **Broadcast Join**: Optimize small tables.
- **Repartition based on Skewed Columns**.

## **11. What is a broadcast variable, and when should you use it?**
- Optimizes **small** lookup tables to avoid large data shuffling.
- Example:
```python
from pyspark.sql.functions import broadcast
df = df1.join(broadcast(df2), "id")
```

## **12. How does PySpark handle caching & persistence?**
- `df.cache()` stores DataFrame in memory.
- `df.persist(storageLevel)` provides more control over caching.
- Example:
```python
df.persist()
```

## **13. Explain the role of PySpark’s Catalyst optimizer.**
- Optimizes execution plan for queries.
- Uses **logical plans** and **physical plans** to optimize execution.

## **14. How do you implement UDFs in PySpark?**
```python
from pyspark.sql.functions import udf
from pyspark.sql.types import IntegerType

def double_value(x):
    return x * 2

double_udf = udf(double_value, IntegerType())
df.withColumn("double_age", double_udf(df["age"])).show()
```

## **15. What is the difference between Pandas UDFs & regular UDFs in PySpark?**
- **Regular UDFs**: Work row-wise but are slower.
- **Pandas UDFs**: Work on Pandas DataFrames and are much faster.

Example using **Pandas UDF**:
```python
from pyspark.sql.functions import pandas_udf
import pandas as pd

def multiply(x: pd.Series) -> pd.Series:
    return x * 2

multiply_udf = pandas_udf(multiply, IntegerType())
df.withColumn("double_age", multiply_udf(df["age"])).show()
```

## **16. How do you debug PySpark jobs?**
- **Use `.explain()`** to analyze execution plan.
- **Use logs & checkpoints**.
- **Enable event logging with Spark UI**.

Example:
```python
df.explain(True)
```

## **17. How do you handle date & timestamp operations in PySpark?**
```python
from pyspark.sql.functions import current_date, date_add

df.withColumn("new_date", date_add(current_date(), 5)).show()
```

## **18. What are accumulator variables in PySpark?**
- Used for counting operations in distributed mode.
- Example:
```python
accum = spark.sparkContext.accumulator(0)
rdd.foreach(lambda x: accum.add(1))
print(accum.value)
```

## **19. How do you execute SQL queries in PySpark?**
```python
df.createOrReplaceTempView("table")
spark.sql("SELECT * FROM table WHERE age > 30").show()
```

# **Advanced PySpark: Taking Your Skills to the Next Level**

## **1. How do you implement structured streaming in PySpark?**
Structured Streaming is a **real-time** data processing engine built on Spark SQL. It processes data incrementally using micro-batches.

### **Example: Read Streaming Data from Kafka**
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import *

spark = SparkSession.builder.appName("StructuredStreaming").getOrCreate()

df = spark.readStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "topic_name") \
    .load()

query = df.selectExpr("CAST(value AS STRING)").writeStream \
    .outputMode("append").format("console").start()

query.awaitTermination()
```
✅ **Key Concepts**
- **Source:** Kafka, File Systems, Socket, etc.
- **Sink:** Console, Kafka, DB, etc.
- **Modes:** Append, Complete, Update

---

## **2. Explain the concept of watermarking in PySpark streaming.**
Watermarking helps **handle late-arriving data** by defining **a time threshold** beyond which late data is ignored.

### **Example: Handling Late Events**
```python
df = df.withWatermark("event_time", "10 minutes") \
       .groupBy("user", window("event_time", "5 minutes")) \
       .count()
```
✅ **Key Concepts**
- **Event Time**: The actual timestamp when an event occurred.
- **Processing Time**: When Spark processes the event.
- **Late Data**: Data that arrives past a specified watermark.

---

## **3. How do you integrate PySpark with Kafka for real-time processing?**
Kafka can be used as both a **source** and **sink** for PySpark streaming.

### **Read from Kafka**
```python
df = spark.readStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "my_topic") \
    .load()
```

### **Write to Kafka**
```python
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)") \
    .writeStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("topic", "output_topic") \
    .start()
```
✅ **Kafka Integration Benefits**
- **Scalability**: Handle large-scale data.
- **Fault-Tolerant**: Supports exactly-once processing.
- **Real-Time Processing**: Works with Spark’s structured streaming.

---

## **4. How does PySpark handle large-scale distributed ML using MLlib?**
MLlib is Spark’s built-in library for **machine learning** at scale.

### **Example: Train a Logistic Regression Model**
```python
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import VectorAssembler

# Convert features into a single vector
assembler = VectorAssembler(inputCols=["feature1", "feature2"], outputCol="features")
df = assembler.transform(df)

# Train model
lr = LogisticRegression(featuresCol="features", labelCol="label")
model = lr.fit(df)
```
✅ **Key MLlib Components**
- **ML Pipelines**
- **Feature Engineering**
- **Classification, Regression, Clustering, and Recommendation Models**

---

## **5. Difference between batch processing and stream processing in PySpark?**
| Feature | Batch Processing | Stream Processing |
|---------|----------------|----------------|
| Processing Type | Processes all data at once | Processes data continuously |
| Latency | High | Low |
| Use Case | Data Warehouses, ETL | Real-time analytics, Fraud detection |
| Example | `spark.read.csv()` | `spark.readStream()` |

---

## **6. How do you optimize join operations for big data?**
### **Optimization Techniques**
1. **Broadcast Join** (for small tables)
   ```python
   from pyspark.sql.functions import broadcast
   df1.join(broadcast(df2), "id").show()
   ```
2. **Bucketing** (Avoid full shuffle)
   ```python
   df.write.bucketBy(4, "id").saveAsTable("bucketed_table")
   ```
3. **Repartitioning** (Optimize shuffle)
   ```python
   df.repartition("join_key")
   ```

---

## **7. How do you write an efficient ETL pipeline using PySpark?**
### **Steps in an ETL Pipeline**
1. **Extract Data** (from databases, APIs, files)
2. **Transform Data** (cleaning, deduplication, joins, aggregations)
3. **Load Data** (write to database, data warehouse)

### **Example: Read, Transform & Write**
```python
df = spark.read.csv("input.csv", header=True)

# Transform
df = df.withColumn("new_col", df["old_col"] * 2).dropDuplicates()

# Load
df.write.mode("overwrite").parquet("output.parquet")
```
✅ **Optimization Tips**
- Use **Parquet instead of CSV**.
- Use **Broadcast Join** when possible.
- Cache intermediate results when needed.
- Partition large datasets for efficiency.

---

This document includes all **Advanced** PySpark interview questions with explanations and examples. 🚀

For further deep-dive into specific topics, feel free to ask! 😃

