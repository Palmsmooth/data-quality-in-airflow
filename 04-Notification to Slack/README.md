# Notification to Slack

Within different DAGs, we can implement callback functions so that when Airflow runs a pipeline—whether it fails or succeeds—it sends a notification. This allows relevant people to be aware of the pipeline status, and if a failure occurs, they can step in to fix it promptly.

For example, in example_ge_operator.py, we did not define an on_failure_callback, so if the pipeline fails, no notification will be sent. On the other hand, in example_sql_data_quality_check.py, we have implemented an on_failure_callback, so a notification will be triggered whenever a failure occurs.

Inside on_failure_callback, we can define any common function we want, depending on where we would like to send the notification. In the example_sql_data_quality_check.py example, we use a common function that sends notifications to Slack. However, this can be extended to other platforms as well, such as Microsoft Teams or LINE, by implementing corresponding functions.


<img src="image\commons.slack_client.png" width="100%" height="40%">

---

## Slack Notification

<img src="image\Slack Notification.png" width="100%" height="40%">

---