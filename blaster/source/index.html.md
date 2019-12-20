### How to run

Needs 2 rabbit queues.

Environment variables, eg.:

```
rabbitmq-out-username=guest;
rabbitmq-password=guest;
maxSendAttempts=1;
GOOGLE_CLOUD_PROJECT=curiouscat-dev-148816;
fcmCredentialsPrefix=curiouscat-dev.json;
awsSecret=thesecret;
rabbitmq-out-password=guest;
rabbitmq-username=guest;
GOOGLE_APPLICATION_CREDENTIALS=/Users/rolandpogonyi/curiouscat-dev.json;
JDBC_CONNECTION_STRING=jdbc:mysql://localhost:3306/blaster?autoReconnect=true&useSSL=false&useUnicode=true&characterEncoding=utf-8;
fcmCredentialsBucket=odr-ss-int;
awsAccess=theawsaccess;
dbUsername=root;
dbPassword=0ndev1ce
```
#### Please note 
In order to start an app or run unit
 tests valid `GOOGLE_APPLICATION_CREDENTIALS` file has to be available. 


### Message from rabbit queue examples

Message header has `respondentPk`

#### Payload

Programmatic push from TE will be channel:task_payout + messageId, messageArgs
```
{
    "respondentPk": "ae00858c-728a-42ea-8dde-d2c54a359000",
    "jobId": 12345,
    "payload": {
        "messageId": "task_complete",
        "messageArgs": ["64"],
        "channel": "task_payout"
    }
}
```

Push  from RPM will typically be channel:new_task + title, message

```
{
    "respondentPk": "ae00858c-728a-42ea-8dde-d2c54a359000",
    "jobId": 12345,
    "payload": {
        "message": "some message",
        "title": "some title",
        "channel": "new_task",
        "data": {}
    }
}
```

General

```
{
    "respondentPk": "3ff5b9cf-64ae-41e3-b360-0d9481f0bd33",
    "jobId": 12345,
    "payload": {
        "message": "some message",
        "title": "some title",
        "channel": "general",
        "data": {}
    }
}
```

Message key for testing (Zack's dev device):
```
fu_KNU7fw2k:APA91bF6E12QKnHLUQsz1xiDTle_Ob8oAfXkKbElG98oPFaDfl3ngz9nTh0UoOsu06aSwT4XRPOkwu8ncCnm4rUqbiyH43sBAYyMWXiIYsgH0Kzsk-zf4Fw_el-3Ae5C-Byuoq0Dnue7
```

FCM auth env vars. Make sure you have the google service json at the specified path.
```
GOOGLE_CLOUD_PROJECT=curiouscat-dev-148816
GOOGLE_APPLICATION_CREDENTIALS=/Users/your_user/curiouscat-dev.json
```

To run with Holster set FCM_HOST

```
FCM_HOST=http://localhost:8090;
```

By default holster is running on port `8090`
Google project has to be defined as per prod settings.
