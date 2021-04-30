---
title: Task Exchange API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Task Exchange API! 

The TE API is a RESTful API for task information for the Curious Cat.  




This page was created with [Slate](https://github.com/lord/slate)

# Setup

## Host
Please note most API's are not publically available. Some are available via apache reverseproxy such as callback urls for task origins.

```shell
# staging
host="https://task-exchange-i.odbot.co.uk"

# production
host="https://pawoints.com"
```

## api versioning

```shell
defaultApiVersion="1"
```

# Authentication

No authentication as this is not publically available. Requests are proxied through RP where required and that takes care of authentication

# Requests

## Task List

List of available tasks based on the specified filter


```shell
    $host/api/1/task?country=GB&respondentPk=dd6fe43a-07a3-4006-80e5-e96437afbf15
```

Query Params

|Param|Mandatory|Description|
|---|---|---|
| country| true| The country to return tasks for |
| respondentPk| true| The respondent pk for the user requesting tasks. Used to find and filter out tasks already completed |
|os|false|OS being used - `ios` or `android` |
|locale|false|The locale of tasks to search for|
|language|false| The language of tasks to search for|
|clientPlatform|false||
|deviceId|false|Used when finding aperture tasks|
|cohort|false|Used to apply experiments around task ranking|
|cpiPointsLimit|false|Maximum number of points that a single task should offer|
|originWhitelist|false|Origins that the task list should be generated from. Defaults to all when not provided|
|taskStartLimit|false|Max number of starts the specified user (respondentPk) can have in the last week. Used to limit the task list so it doesn't show more than they could take part in.|
|taskCompleteLimit|false|Max number of successful the specified user (respondentPk) can have in the last week.  Used to limit the task list so it doesn't show more than they could take part in.|
|quality|false|The users current quality. Used for extra filtering such as not allowing low quality users to start tasks with a low number of completes|
|risk|false|The users current risk. Used for extra filtering such as not allowing high risk users to start tasks with a low number of completes|

