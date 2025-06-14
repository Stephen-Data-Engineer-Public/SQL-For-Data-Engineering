# SQL Windows Function

Picture your data like a long train, where each coach represents a specific group—such as a department, category, or region.

**Window Functions** let you apply calculations (like totals, averages, or rankings) **within each coach** separately, without breaking the train apart. The magic is: it's still one unified dataset, but each part gets its own focused analysis.

## Types of Window Functions
 The windowing functions can be divided into several types:
- **Ranking functions:** This type of function adds a ranking for each row or divides the rows into buckets. The include: _ROW_NUMBER_, _RANK_, _DENSE_RANK_, and _NTILE_.
- **Window aggregates:** This function allows you to calculate summary values in a non-aggregated query. The include: _MIN()_, _MAX()_, 
- **Accumulating aggregates:** Accumulating aggregates are just aggregate window functions (like SUM, AVG, COUNT) with a window frame that grows; meaning it accumulates values over a defined range.
- **Analytic (Offset) functions:** Several new scalar functions, four of which are almost magical!

### Ranking functions:

#### **Imagine Your Data as a Long Train**

Each **coach** in the train represents a **partition** or group—like a department, product category, or geographic region. Inside each coach are **passengers (rows of data)** that need to be ranked.

#### 1. **ROW\_NUMBER()** – Strict Seat Numbering

Each passenger gets a **unique seat number** in order.

* Even if multiple passengers have the same attributes (like score or salary), they still get **different, sequential numbers**.
* **Use when you need a strict order, no ties allowed.**

Example:
If 3 people tie for 1st place, they’ll get:

```
1, 2, 3
```

#### 2. **RANK()** – Honors Equal Performers

Passengers with the **same score** get the **same rank**, but the **next rank skips accordingly**.

* Like giving 3 people gold medals and skipping silver and bronze.

Example:
If 3 people tie for 1st place, the ranks will be:

```
1, 1, 1, 4
```

#### 3. **DENSE\_RANK()** – No Gaps Between Winners

Same as `RANK()`, but it **doesn't skip ranks**.
* Think of a coach where tied passengers still get equal status, but the numbering **remains dense**.

Example:
If 3 people tie for 1st place, the ranks will be:
```
1, 1, 1, 2
```

#### 4. **NTILE(n)** – Divide the Coach into Equal Sections

Splits the passengers **evenly across `n` parts** (like assigning each to a cabin).
* If you want to divide the group into **quartiles, deciles**, etc., this is your tool.

Example:
In a coach with 100 passengers and `NTILE(4)`, each 25 gets assigned to:

```
1st quartile → 1, 2nd quartile → 2, ... up to 4
```



## Window Frames


###      Window Functions vs GROUP BY 
Both  **Window Functions** and **GROUP BY** are used for grouping and aggregation—but they work differently.

**Sample data**

| employee | department | sales |
|----------|------------|-------|
| Alice    | A          | 100   |
| Bob      | A          | 200   |
| Carol    | B          | 150   |
| Dave     | B          | 300   |
#### **Group By**
- Aggregates data by collapsing rows into summary rows.
- You only get one result per group.
- Ideal when you just want totals or averages per group.

Example:
```sql
SELECT department, SUM(sales) AS total_sales
FROM sales
GROUP BY department;
```
This returns one row per department—you no longer see individual employees.

|department | total_sales|
|-----------|------------|
|    A      |     300    |
|    B      |     450    |


**Window Functions**
- Perform calculations across a group of rows (a window) but preserve each individual row.
- You can still see all columns and all rows.
- Ideal for things like rankings, running totals, or comparing a row to its group.

Example:

```sql
SELECT employee, department, sales,
       SUM(sales) OVER (PARTITION BY department) AS dept_total_sales
FROM sales;
```
This shows every employee, plus their rank within their department—without collapsing the data.


|employee | department | sales | dept_total_sales|
|---------|----------- |------ | ----------------|
|  Alice  |     A      |  100  |     300         |
|  Bob    |     A      |  200  |     300         |
|  Carol  |     B      |  150  |     450         |
|  Dave   |     B      |  300  |     450         |

**Reminder:**
The query first gathers data (**_FROM_**), filters rows (**_WHERE_**), groups data (**_GROUP BY_**), and then computes values (**_SELECT_**).

Window functions only appear during the "compute" step, so trying to use them earlier is like asking for the result of a calculation before it’s been done.
