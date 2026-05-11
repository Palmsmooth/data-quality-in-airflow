# Simple Data Reconciliation

<img src="image\Data Reconciliation.png" width="100%" height="40%">

All the concepts we discussed can be combined into a single end-to-end pipeline. A reference implementation can be found in example_end_to_end.py, which defines an end-to-end DAG called order_to_bigquery.

In this DAG, the workflow is structured as follows:

- extract_data: This task extracts data from SQLite.
- transform_data: Using a PythonOperator, the data is transformed and cleaned (e.g., removing invalid or dirty data). During this step, we can also compute metrics such as row count.
- Reconciliation / validation layer:

  - We can implement a reconciliation_checks task to validate data consistency. For example, we compare row counts before and after transformation using a simple assertion:
  
    ```sh
    assert cnt_order_transform == 100, "Check number of rows count"
    ```

    This ensures that the source and transformed data have consistent row counts.

  - Alternatively, we can use reusable common SQL-based validation checks.
- load_to_temp_table: Load the transformed data into a temporary table in BigQuery.
- Data quality checks:
  - Before loading into the final table, we can run validation checks (e.g., using BigQueryColumnCheckOperator) on the temporary table.
- merge_data: Once validation passes, data is merged from the temporary table into the final BigQuery table.

By chaining these tasks into a single flow—start → extract_data → transform_data → load_to_temp_table → validation/reconciliation → merge_data → end—we achieve an integrated pipeline that includes both data validation and data reconciliation.

An alternative pattern is to first load data into a temporary table, perform all validation and reconciliation checks there, and only merge into the final table once the data is confirmed to be correct. This approach helps ensure data quality before it reaches the destination system.

---