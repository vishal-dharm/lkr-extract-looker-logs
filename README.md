## Overview

A Python script that extracts Looker system/audit logs from [System Activity](https://docs.looker.com/admin-options/system-activity) and exports the Logs to Cloud Logging. This example tries to format the output logs like a [GCP Audit Log](https://cloud.google.com/logging/docs/audit/understanding-audit-logs) as best as possible. See [mapping](#gcp-audit-log-fields-to-looker-system-activity-mapping) for comparison between Looker System Activity Fields and GCP Audit Log Fields

## Requirements
- Looker Instance in which you have Admin or `see_system_acitvity` permission
- Google Cloud Project with Cloud Logging API enabled
- [Python 3.6+](https://github.com/looker-open-source/sdk-codegen/tree/main/python#sample-project-setup) which is required by the Python Looker SDk

## Deployment

- Clone the repo
  ```
  clone then cd into it
  ```

- Install dependencies 
  ```
  pip install looker-sdk
  pip install --upgrade google-cloud-logging
  ```

- Create API credentials and set environment variables
  ```
  export LOOKERSDK_BASE_URL="<Your API URL>"
  export LOOKERSDK_CLIENT_ID="<Your Client ID>"
  export LOOKERSDK_CLIENT_SECRET="<Your Client Secret>"
  ```

- Configure gcloud and [setup service account](https://cloud.google.com/logging/docs/reference/libraries#setting_up_authentication) to write Logs to Cloud Logging
  ```
  gcloud config set project <Project ID>
  export GOOGLE_APPLICATION_CREDENTIALS="Service Account Key Path"
  ```

- Run `main.py`
  ```
  python3 main.py
  ```


## GCP Audit Log Fields to Looker System Activity Mapping

| GCP Audit Log Field       | Looker System Actvity Field |
| -----------               | -----------                 |
| [logName](https://cloud.google.com/logging/docs/reference/v2/rest/v2/LogEntry#:~:text=Fields-,logName,-string) | `looker_system_activity_logs` |
| [timestamp](https://cloud.google.com/logging/docs/reference/v2/rest/v2/LogEntry#:~:text=reported%20the%20error.-,timestamp,-string) | [event.created](https://docs.looker.com/admin-options/tutorials/events#:~:text=for%20example%2C%20create_dashboard-,created,-Date%20and%20time) |
| [resource.type](https://cloud.google.com/logging/docs/reference/v2/rest/v2/MonitoredResource#:~:text=Fields-,type,-string)  | `looker_system_activity_logs`  |
| [resource.type](https://cloud.google.com/logging/docs/reference/v2/rest/v2/MonitoredResource#:~:text=Fields-,type,-string)  | `looker_system_activity_logs`  |
| [insertId](https://cloud.google.com/logging/docs/reference/v2/rest/v2/LogEntry#:~:text=is%20LogSeverity.DEFAULT.-,insertid,-string)  | [event.id](https://docs.looker.com/admin-options/tutorials/events#:~:text=Description-,id,-Unique%20numeric%20identifier)  |
| `protoPayload.status` | [event.attribute.status](https://docs.looker.com/admin-options/tutorials/events#:~:text=Trigger-,Attributes,-add_external_email_to_scheduled_task) |
| `protoPayload.authenticationInfo`  | [event.user_id](https://docs.looker.com/admin-options/tutorials/events#:~:text=of%20the%20event-,user_id,-Unique%20numeric%20ID), [event.sudo_user_id](https://docs.looker.com/admin-options/tutorials/events#:~:text=for%20example%2C%20dashboard-,sudo_user_id,-Unique%20numeric%20ID)  |
| `protoPayload.authorizationInfo`  | `permission_set.permissions`  |
| `protoPayload.methodName`  | [event.name](https://docs.looker.com/admin-options/tutorials/events#:~:text=triggered%20the%20event-,name,-Name%20of%20the) |
| `protoPayload.response` | [event_attributes](https://docs.looker.com/admin-options/tutorials/events#:~:text=Trigger-,Attributes,-add_external_email_to_scheduled_task) |