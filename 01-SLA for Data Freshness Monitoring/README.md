# SLA for Data Freshness Monitoring

SLA does not cause the pipeline to fail. The pipeline will continue to run normally. In other words, even if data freshness does not meet expectations—meaning the data arrives later than expected—the pipeline itself is not considered failed.

For example, a pipeline that is scheduled to run every 3 minutes may experience delays, but those delays do not trigger a pipeline error.

To monitor data freshness, you can go to the Browse tab and navigate to SLA Misses. There, you will see notifications indicating that a DAG (e.g., example_sla_dag) has missed its SLA, meaning the data arrived later than the defined expectation.

In Apache Airflow, you can also implement a custom notification plugin. For instance, when an SLA miss occurs, the system can automatically send alerts to Slack or any other messaging channel.

<img src="image\example_sla_dag.png" width="100%" height="40%">
<img src="image\status.png" width="100%" height="40%">
<img src="image\SLA Misses.png" width="100%" height="40%">
<img src="image\notification.png" width="100%" height="40%">

---