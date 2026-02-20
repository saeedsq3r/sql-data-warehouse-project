Great — here is a **practical Pandas vs PySpark Cheat Sheet** focused on:

* Transformations
* Joins
* GroupBy
* Pivot
* Window functions
* Unique functions (different ones too)

This is exactly what Data Engineers use in real projects.

---

# 🧾 Pandas vs PySpark Functions Cheat Sheet (DataFrame API)

## 🔹 1. Reading Data

| Task         | Pandas                    | PySpark                                                     |
| ------------ | ------------------------- | ----------------------------------------------------------- |
| Read CSV     | `pd.read_csv("file.csv")` | `spark.read.csv("file.csv", header=True, inferSchema=True)` |
| Read Parquet | `pd.read_parquet()`       | `spark.read.parquet()`                                      |
| Read Excel   | `pd.read_excel()`         | ❌ Not native (needs extra libs)                             |

---

# 🔹 2. Basic Inspection

| Task      | Pandas          | PySpark                       |
| --------- | --------------- | ----------------------------- |
| Show data | `df.head()`     | `df.show()`                   |
| Shape     | `df.shape`      | `df.count(), len(df.columns)` |
| Columns   | `df.columns`    | `df.columns`                  |
| Info      | `df.info()`     | `df.printSchema()`            |
| Describe  | `df.describe()` | `df.describe().show()`        |

---

# 🔹 3. Column Selection & Filtering (Transformations)

| Task            | Pandas                   | PySpark                  |
| --------------- | ------------------------ | ------------------------ |
| Select column   | `df["col"]`              | `df.select("col")`       |
| Select multiple | `df[["a","b"]]`          | `df.select("a","b")`     |
| Filter rows     | `df[df["age"]>20]`       | `df.filter(df.age > 20)` |
| Query           | `df.query("age > 20")`   | `df.filter("age > 20")`  |
| Drop column     | `df.drop("col", axis=1)` | `df.drop("col")`         |

---

# 🔹 4. Adding / Modifying Columns (Major Difference)

| Task          | Pandas                         | PySpark                                    |
| ------------- | ------------------------------ | ------------------------------------------ |
| New column    | `df["new"]=df["a"]*2`          | `df.withColumn("new", col("a")*2)`         |
| Rename column | `df.rename(columns={"a":"A"})` | `df.withColumnRenamed("a","A")`            |
| Type casting  | `df["a"].astype(int)`          | `df.withColumn("a", col("a").cast("int"))` |

Note: PySpark uses `withColumn()` instead of direct assignment.

---

# 🔹 5. GroupBy & Aggregations

| Task         | Pandas                                            | PySpark                                          |
| ------------ | ------------------------------------------------- | ------------------------------------------------ |
| Groupby mean | `df.groupby("dept")["sal"].mean()`                | `df.groupBy("dept").avg("sal")`                  |
| Multiple agg | `df.groupby("d").agg({"sal":"mean","age":"max"})` | `df.groupBy("d").agg({"sal":"avg","age":"max"})` |
| Count        | `df.groupby("d").size()`                          | `df.groupBy("d").count()`                        |

---

# 🔹 6. Joins / Merge (Very Similar Concept)

| Task       | Pandas                                     | PySpark                               |
| ---------- | ------------------------------------------ | ------------------------------------- |
| Inner join | `pd.merge(df1, df2, on="id", how="inner")` | `df1.join(df2, on="id", how="inner")` |
| Left join  | `how="left"`                               | `how="left"`                          |
| Right join | `how="right"`                              | `how="right"`                         |
| Outer join | `how="outer"`                              | `how="outer"`                         |

Concept is identical.

---

# 🔹 7. Sorting & Ordering

| Task        | Pandas                  | PySpark                      |
| ----------- | ----------------------- | ---------------------------- |
| Sort values | `df.sort_values("age")` | `df.orderBy("age")`          |
| Desc sort   | `ascending=False`       | `orderBy(col("age").desc())` |

---

# 🔹 8. Pivot & Reshaping

| Task        | Pandas                                                              | PySpark                               |
| ----------- | ------------------------------------------------------------------- | ------------------------------------- |
| Pivot table | `df.pivot_table(index="d", columns="p", values="s", aggfunc="sum")` | `df.groupBy("d").pivot("p").sum("s")` |
| Melt        | `pd.melt()`                                                         | ❌ No direct (use `stack`/SQL)         |

Pivot exists in both but syntax differs.

---

# 🔹 9. Handling Missing Values

| Task       | Pandas         | PySpark                        |
| ---------- | -------------- | ------------------------------ |
| Drop nulls | `df.dropna()`  | `df.dropna()`                  |
| Fill nulls | `df.fillna(0)` | `df.fillna(0)`                 |
| Check null | `df.isnull()`  | `df.selectExpr("col IS NULL")` |

---

# 🔹 10. Duplicates & Unique

| Task            | Pandas                 | PySpark                       |
| --------------- | ---------------------- | ----------------------------- |
| Drop duplicates | `df.drop_duplicates()` | `df.dropDuplicates()`         |
| Unique values   | `df["col"].unique()`   | `df.select("col").distinct()` |

---

# 🔥 11. APPLY vs UDF (VERY IMPORTANT DIFFERENCE)

| Task           | Pandas                   | PySpark          |
| -------------- | ------------------------ | ---------------- |
| Row-wise apply | `df.apply(func, axis=1)` | Use UDF (slower) |
| Vectorized ops | Fast                     | Recommended      |

PySpark Example:

```python
from pyspark.sql.functions import udf
```

Better approach:

```python
df.withColumn("new", col("a")*2)
```

Avoid UDF when possible.

---

# 🚀 12. Window Functions (BIG Advantage in PySpark)

| Task         | Pandas                        | PySpark                  |
| ------------ | ----------------------------- | ------------------------ |
| Rank         | `df["r"]=df["sal"].rank()`    | `rank().over(window)`    |
| Rolling mean | `df["sal"].rolling(3).mean()` | `avg().over(window)`     |
| Lag/Lead     | Limited                       | `lag(), lead()` built-in |

PySpark uses powerful SQL-style windows from **Apache Spark**.

---

# ❗ 13. Functions That Exist in PySpark but Not Properly in Pandas

These are **extra powerful features**:

| Feature                | PySpark Function     | Pandas Status    |
| ---------------------- | -------------------- | ---------------- |
| Lazy execution         | DAG execution        | ❌ Not available  |
| Distributed processing | Native               | ❌ Single machine |
| SQL queries            | `spark.sql()`        | ❌ Not native     |
| Partitioning           | `repartition()`      | ❌ Not available  |
| Caching                | `cache(), persist()` | ❌ Not needed     |
| Broadcast join         | `broadcast()`        | ❌ Not available  |

---

# ⚠️ 14. Functions in Pandas but Different/Weak in PySpark

| Feature              | Pandas                       | PySpark                    |
| -------------------- | ---------------------------- | -------------------------- |
| Indexing (iloc, loc) | Powerful                     | Limited                    |
| Excel support        | Native                       | Weak                       |
| Small data speed     | Very fast                    | Slower overhead            |
| Visualization        | Direct (matplotlib, seaborn) | Needs conversion to Pandas |

---

# 🧠 15. Mental Mapping (Best Way to Remember)

| Concept     | Think in Pandas | Think in PySpark   |
| ----------- | --------------- | ------------------ |
| Transform   | Vector ops      | `withColumn()`     |
| Aggregate   | `groupby()`     | `groupBy()`        |
| Merge       | `merge()`       | `join()`           |
| Apply logic | `apply()`       | Column expressions |
| Execution   | Instant         | Lazy until action  |

---

# 🎯 Final Advice (For Data Engineering & Databricks)

Since you already know:

* Pandas
* PySpark
* Databricks

You are in a **very strong position** because:

> 70–80% of DataFrame logic is transferable between Pandas and PySpark.

Just remember the 3 critical differences:

1. No index in PySpark
2. Use `withColumn()` instead of assignment
3. Lazy execution (needs `.show()`, `.count()`, `.collect()`)

---

If you want, I can next give you an **“industry-level mapping”** like:

* Pandas EDA workflow → PySpark production pipeline
  (This is what companies actually expect in interviews.)
