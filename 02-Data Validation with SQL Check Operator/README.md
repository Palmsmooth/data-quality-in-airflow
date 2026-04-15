# Data Validation with SQL Check Operator

## Common SQL Provider

1. SQLCheckOperator: Used to validate data quality using SQL queries
2. SQLTableCheckOperator: Used to perform data quality checks at the table level
3. SQLColumnCheckOperator: Used to perform data quality checks at the column level
4. SQLValueCheckOperator: Used to validate data correctness by comparing query results with expected values
5. SQLThresholdCheckOperator: Used to validate data correctness by checking whether values fall within defined min/max thresholds
6. SQLIntervalCheckOperator: Used to validate data by comparing it against historical time intervals

---

## example_sql_check_operator.py

In the DAG defined in example_sql_check_operator.py, you can see the use of an EmptyOperator. This operator is typically used as a placeholder to represent logical start and end points, helping improve the readability and structure of the DAG.

In this example, the DAG is intentionally kept simple, consisting only of a start, an end, and intermediate tasks. There are no tasks related to data ingestion or data transformation. This design reflects a standalone data quality validation DAG, meaning the workflow is dedicated solely to validating data quality.

Within the set of common SQL operators, there are operators specifically designed for data quality checks. The first one used here is the SQLCheckOperator. This operator connects to a database using the connection ID my_retail_database.

The purpose of SQLCheckOperator is to validate whether certain conditions in the data are met. In this case, it checks whether there are any orders created after January 1, 2023. The operator works by executing a SQL query, and the result of that query determines whether the check passes or fails.

In terms of execution, we created a DAG named example_sql_check_operator. When we run the command docker compose up, it sets up an Airflow environment on our local machine.

After that, when we open the example_sql_check_operator DAG in the Airflow UI, we can view various statuses of the DAG, such as whether each task is running, successful, or failed. This allows us to clearly monitor and track the workflow execution.

<img src="image\example_sql_check_operator.png" width="100%" height="40%">
<img src="image\example_sql_check_operator status.png" width="100%" height="40%">

---

## example_sql_data_quality_check.py

There will be another pipeline called example_sql_data_quality_check.py. This pipeline demonstrates the use of multiple operators, including:

- SQLCheckOperator
- SQLColumnCheckOperator
- SQLTableCheckOperator
- SQLValueCheckOperator
- SQLThresholdCheckOperator
- SQLIntervalCheckOperator

Starting with SQLCheckOperator, you can write custom SQL queries to validate data quality.

Next, SQLColumnCheckOperator does not require writing SQL. Instead, you specify the column names you want to validate and define rules such as unique_check or null_check.

For SQLTableCheckOperator, you only need to define a check_statement without writing full SQL queries.

SQLValueCheckOperator allows you to write a SQL query and define an expected result value that the query output should match.

With SQLThresholdCheckOperator, you write a SQL query and define thresholds such as min_threshold or max_threshold to validate whether the result falls within an acceptable range.

Finally, SQLIntervalCheckOperator allows you to perform data quality checks against historical data. For example, setting days_back = -1 compares the current data with data from one day ago (i.e., yesterday).

<img src="image\example_sql_data_quality_check.png" width="100%" height="40%">
<img src="image\example_sql_data_quality_check status.png" width="100%" height="40%">

---