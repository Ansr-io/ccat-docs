Env vars for startup


```
rabbitmq-username=blaster;
rabbitmq-password=blaster;
JDBC_CONNECTION_STRING=jdbc:mysql://localhost:3306/blaster?autoReconnect\=true&useSSL\=false;
dbUsername=root;
dbPassword=root;
HOLSTER_REPORT_BUCKET=holster_report_bucket

```

If `HOLSTER_REPORT_BUCKET` is defined run report will be saved on s3.

API

####Start

```bash
curl host/api/start
```
Optional add `errors` parameter for FCМ service to be throwing errors

```bash
curl host/api/start?errors=true
```

Returns 201 if new run was created or 200 if previous run has not finished yet.


####List

```bash
curl host/api/list
```

Returns list of ids of completed runs.

####Describe

```bash
curl host/api/describe/${run}
```  

Returns JSON description of the run.
  
example:
```bash
curl http://localhost:8090/api/describe/2019-11-29T10:46:06.687
```
response:
```json
{"status":200,
 "run": {"is_run_successful":true,
 "messages_skipped":901,
 "fraud_lock":79,
 "run_id":"2019-11-29T10:46:06.687",
 "total":1610,
 "unregistered":61,
 "preferences_notifications_payout_off":402,
 "preferences_notifications_new_task_off":359},
 }
```
