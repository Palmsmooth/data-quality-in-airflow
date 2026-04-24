# Data Validation with Great Expectation

## Great Expectation Component

<img src="image\Great Expectation Component.png" width="100%" height="40%">

Assume that we have already imported the library. When using Great Expectations operators, we need to define a Data Context, which specifies where the Great Expectations environment is located. This can be configured to run on cloud storage such as Google Cloud Storage (GCS), or on a local filesystem. In this example, we configure the Data Context to reside on a local path.

The Data Context contains configuration files that define how data quality validation connects to specific data sources and data assets. It also includes batch definitions, which determine how each validation run is executed against a specific slice or batch of data.

For data quality checks, instead of writing long SQL queries, we define expectation rules. These rules describe the expected conditions of the data, such as:

- A column should not contain null values
- A column should have unique values

Multiple expectation rules are grouped together into a single unit called an Expectation Suite, which represents the full set of data quality checks for a given table.

When orchestrated with Airflow, each run triggers a validation process. During execution, Great Expectations generates:

- Validation results for each run
- Validation definitions and metadata
- Checkpoints that record execution details (e.g., timestamps)

Additionally, we can define actions, such as sending notifications if a validation fails.

Finally, Great Expectations produces a validation report, which provides a summary of all validation runs, including how many times validations were executed, and whether they succeeded or failed.

---

## Great Expectation Configuration

<img src="image\Great Expectation Configuration.png" width="100%" height="40%">

For the setup process, we need to start by configuring the data source before we can use Great Expectations.

In the docker-compose.yaml file, there is a volume mounting configuration. Mounting volumes means mapping directories from the host machine into the Docker container’s virtual environment. This allows the container to access specific folders from the local filesystem.

In this case, one important folder used by Great Expectations is the include directory. When the Docker container is created, it is connected (mounted) to the local include folder. Inside this folder, there is a subdirectory called gx.

During the initial setup, we only need to create a single file: great_expectations.yml. The remaining folders and structure will be automatically generated when the Airflow task—specifically the Great Expectations operators—runs for the first time.

The great_expectations.yml file acts as the central configuration file for Great Expectations. It defines all necessary settings and essentially functions as a complementary system that enhances the capabilities of Apache Airflow.

Within this configuration file, we specify:

- The datasources used for data quality validation
- Required context configurations so that Great Expectations operators can function correctly when invoked
- The output paths for storing validation results and documentation

The validation output is referred to as Data Docs in Great Expectations terminology. In this setup, the great_expectations.yml file specifies that Data Docs should be stored in the uncommitted directory (i.e., excluded from version control such as Git), specifically under data_docs/local_site.

---

## Great Expectation Operator

<img src="image\Great Expectation Operator.png" width="100%" height="40%">

Once we have the Great Expectations library integrated with Apache Airflow, we can use the GreatExpectationsOperator to run data validation tasks within our DAG.

We can define a custom task_id for each task, as usual in Airflow.

To use Great Expectations, we must specify the path to MY_GX_DATA_CONTEXT. This path points to the directory containing the great_expectations.yml file, which serves as the central configuration for Great Expectations.

Additionally, we need to define the data_asset_name, which refers to the table (or dataset) that we want to validate. This name must match the data assets defined in the great_expectations.yml configuration.

In order to successfully run validations, the data source must already be configured in the great_expectations.yml file. Only after the datasource is properly set up can Great Expectations connect to the data and execute validation checks on the specified data asset.

---

## Expectations Suite

### orders_table_validation_suite.json

<img src="image\orders_table_validation_suite json.png" width="100%" height="40%">

This is an example of an Expectation Suite, which is a file that groups multiple test cases used to validate data quality.

Compared to a common SQL-based approach, an Expectation Suite does not require writing SQL queries. Instead, we define validation logic through configuration.

For example, in orders_table_validation_suite, there is only one expectation defined:
the table row count is expected to be between 2 and 1000.

### orders_columns_validation_suite.json

<img src="image\orders_columns_validation_suite json.png" width="100%" height="40%">

This is an example of a file that contains multiple validations. Several expectations are grouped together into a single Expectation Suite.

In other words, an Expectation Suite is a collection of multiple validation rules used to comprehensively check the data quality of a dataset.

---

## example_ge_operator.py

<img src="image\example_ge_operator py.png" width="100%" height="40%">

In actual usage, inside our DAG folder, we need to import the operator using:

from great_expectations_provider.operators.great_expectations import GreatExpectationsOperator

The GreatExpectationsOperator is a standalone component, meaning it acts as a pipeline step dedicated solely to data quality validation.

From the code, we can see an example task:

- task_id="validate_orders_table_level"

In this task, we specify the data_context_root_dir, which points to include/gx. This is the directory where the great_expectations.yml file is stored and where all expectation configurations are managed.

For table-level validation, we use the parameter expectation_suite_name, set to orders_table_validation_suite.

Another task example is:

- task_id="validate_orders_columns_level"

This task uses the same data_context_root_dir (include/gx), but references a different expectation suite. This suite contains multiple validation rules, typically focused on column-level checks.

---

## Airflow UI

<img src="image\example_ge_operator.png" width="100%" height="40%">

<img src="image\example_ge_operator status.png" width="100%" height="40%">

---

## Great Expectation Data Doc

ทุกครั้งที่เรา run ถ้ามันมีข้อมูลผิดพลาด มันก็จะแจ้ง notification error มาที่ช่องทางที่เรา monitor อยู่ แต่ข้อดีของ Great Expectation มันมี build in ของตัว report ตัว documentation ให้ดู ซึ่งมันจะมีหน้า Data Docs ซึ่งมันจะเก็บสถิติไว้หมดเลย ว่า run validation ไป ตอนไหนบ้าง ใช้ validation suite ว่าอะไรบ้าง มีการเก็บสถิติไว้หมด แล้วก็เก็บ status ไว้ด้วย

validate_orders_table_level


<img src="image\validate_orders_table_level.png" width="100%" height="40%">

<img src="image\validate_orders_table_level status.png" width="100%" height="40%">

<img src="image\validate_orders_columns_level.png" width="100%" height="40%">

<img src="image\validate_orders_columns_level status.png" width="100%" height="40%">

---