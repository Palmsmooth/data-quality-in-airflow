# Data Engineering Project: Data Quality in Airflow
## Project Overview

This project aims to develop a data quality monitoring and validation system for workflows in Apache Airflow. The system focuses on ensuring data reliability by implementing automated checks such as data freshness (SLA monitoring), SQL-based validations, and integrations with Great Expectations for advanced data validation.

It also includes alerting mechanisms (e.g., Slack notifications) to promptly inform stakeholders of any data issues, as well as data reconciliation processes to verify data consistency across sources. Additionally, the project incorporates unit testing to ensure the robustness and correctness of Airflow DAGs, enabling accurate, complete, and trustworthy data pipelines.

---

## Folder Explaination 
```md
data-quality-with-apache-airflow/
│
├── README.md
└── assets/
└── dags/
└── documents/
└── prompts/
└── cred/
└── tests/
└── include/
```

| Name | Description |
| - | - |
| assets/ | Folder for storing assets such as images or diagrams |
| dags/ | Folder for storing DAG code or Airflow Data Pipelines created for the workshop |
| docker-compose.yaml | Docker Compose file used to run Airflow locally |
| prompts/ | Folder for storing prompts used for generating code or queries |
| cred/ | Folder for storing credential or configuration files, such as sa.json |
| tests/ | Folder for storing unit test files for testing Python code |
| include/ | Folder for storing common modules or external integrations with open-source tools (e.g., Great Expectations) |

---

## Getting Start

### 1.SLA for Data Freshness Monitoring 
### 2.Data Validation with SQL Check Operator
### 3.Data Validation with Great Expectation
### 4.Notification to Slack
### 5.Simple Data Reconciliation
### 6.Apache Airflow Unit Test

---