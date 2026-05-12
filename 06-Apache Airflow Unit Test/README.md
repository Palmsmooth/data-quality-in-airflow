# Apache Airflow Unit Test

## Python UnitTest with Pytest

<img src="image\Python UnitTest with Pytest.png" width="100%" height="40%">

In a data pipeline, unit testing is typically done by writing small test functions that validate individual components of the pipeline.

For example, a pipeline may consist of multiple tasks such as ingest, extract, and transform. Some of these tasks—especially in the extract or transform phase—might use a PythonOperator to execute functions for data cleaning, calculations, or calling external APIs. These smaller functions (often implemented as common/shared functions in Airflow) can all be unit tested independently.

In Python, there are two main libraries commonly used for unit testing:

- pytest
- unittest

In this case, we focus on pytest, which is a popular and flexible testing framework for writing unit tests.

Example concept (based on the image)

A data engineer writes a simple function:

```sh
def func(x):
    return x + 1
```

Then creates a test function to validate it:

```sh
def test_answer():
    assert func(3) == 5
```

Here, unit testing means using one function (test_answer) to validate another function (func). The assert statement defines the expected outcome.

- Expected: func(3) should return 5
- Actual: func(3) returns 4

Since the result does not match the expectation, the test fails. This is a simple example of an assertion failure.

Applying this to data pipelines

We can apply the same concept to all functions used in a data pipeline:

- Data cleaning functions → assert cleaned output is correct
- Transformation logic → assert calculated values match expectations
- API calls → assert response status (e.g., 200 OK)
- Data extraction logic → assert schema or row count

For example:

```sh
def test_api_call():
    result = call_api()
    assert result.status_code == 200
```

If the API returns 404 Not Found, the test fails immediately. This allows us to detect issues early—before the pipeline is deployed or executed in production.

Key idea

Unit testing ensures that each function in the pipeline works correctly in isolation. By catching errors early at the function level, we reduce the risk of failures when the entire Airflow pipeline runs.

---

## Example: Simple Test

<img src="image\temperature_converter.png" width="100%" height="40%">

Suppose we have a common function that will be reused in our data pipeline, for example temperature_converter.py. Imagine a pipeline that retrieves temperature data from an API (in Celsius), and we need to convert it to Fahrenheit using a shared utility function.

We want to ensure that this function works correctly before integrating it into the pipeline. For example:

```sh
def celsius_to_fahrenheit(celsius):
    return (celsius * 9/5) + 32
```

This function can be reused as a module inside our Airflow pipeline, but more importantly, we should unit test it independently.

Organizing unit tests

It is recommended to store all unit tests in a dedicated folder called:

```sh
tests/
```

In this case, we have a test file named:

```sh
test_temperature_converter.py
```

Writing test cases with pytest

We can write assertions to validate expected outputs. For example:

```sh
def test_celsius_to_fahrenheit():
    assert celsius_to_fahrenheit(0) == 32
    assert celsius_to_fahrenheit(100) == 212
```

This means:

If input is 0°C, the expected output should be 32°F
If input is 100°C, the expected output should be 212°F

- These are test cases that validate correctness.

- Handling floating-point precision

For cases where precision matters, we use pytest.approx:

```sh
assert pytest.approx(celsius_to_fahrenheit(25), 0.1) == 77
```

This allows for small floating-point rounding differences.

Extending to all conversion functions

We can apply the same approach to all functions in temperature_converter.py, such as:

- fahrenheit_to_celsius
- celsius_to_kelvin
- kelvin_to_celsius
- fahrenheit_to_kelvin
- kelvin_to_fahrenheit

Each function should have its own test cases to ensure correctness.

Key idea
- These utility functions are building blocks of your data pipeline.
- By unit testing them in isolation, you ensure they behave correctly before being used in Airflow tasks.
- If any test fails (e.g., wrong conversion result), you can catch the issue early during development instead of during pipeline execution.

This approach improves reliability and confidence in your overall data pipeline.

<img src="image\checkmark icon.png" width="100%" height="40%">

<img src="image\Test Result Run individual test functions.png" width="100%" height="40%">

<img src="image\quickly validate everything with a single click.png" width="80%" height="40%">

<img src="image\Test Result the entire test suite.png" width="100%" height="40%">

In Visual Studio Code, you’ll notice a checkmark icon appearing next to each def (test function). When you click it, VS Code will re-run that specific test immediately.

If you have multiple test files, all of them will be listed in the Testing panel in VS Code. From there, you can:

- Run individual test functions
- Run all tests within a file
- Or execute the entire test suite across all files

This makes it very convenient for developers—especially those who have already written unit tests—to quickly validate everything with a single click.

---

## Example: Test DAG integrity

<img src="image\Test DAG integrity validate everything with a single click.png" width="80%" height="40%">

<img src="image\Test DAG integrity error.png" width="100%" height="40%">

Another best practice that Airflow strongly supports is DAG integrity testing.

Airflow provides a module called airflow.models.DagBag, which allows us to validate all DAGs in our dags/ folder locally, even before deploying them to an Airflow environment.

Why this is useful

For example, if you develop DAGs on your local machine and plan to upload them to GCS to run on Cloud Composer, you can run DAG integrity tests beforehand. This helps catch issues early without needing a full Airflow runtime environment.

You simply point DagBag to your DAG folder, and it will automatically validate all DAG files for correctness before deployment to production.

Concept of DagBag

DagBag can be thought of as a container that loads all DAGs and performs validation checks on them, such as:

- Ensuring there is at least one DAG (i.e., pipelines exist)
- Verifying there are no import errors across DAG files
- Checking that DAG IDs are unique
- Ensuring task IDs are unique within each DAG
- Validating that all tasks use valid operator types
- Ensuring task configurations are properly defined
- Checking that task parameters are valid

Example: test_airflow_dags_integrity.py

Using pytest, we can implement these checks as unit tests:

```sh
from airflow.models import DagBag

def test_dagbag_no_errors(dagbag):
    assert len(dagbag.import_errors) == 0
```

This test asserts that there should be no import errors. If, for example, there are 2 import errors, the test will fail, and we immediately know there are issues to fix.

Key benefits
- Run all DAG validation tests locally (no need to deploy first)
- Detect errors early (e.g., broken imports, duplicate IDs, invalid configs)
- Improve reliability before production deployment
- Integrate easily with CI/CD pipelines

Important note

Although this works locally, you should ensure that your local environment matches the production environment (e.g., same Airflow version, dependencies). Otherwise, some issues might only appear after deployment.

---