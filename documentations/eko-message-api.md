# Eko Message API Documentation

### Prerequisites

| Parameters | Descriptions |
| ---------- | ------------ |
| API endpoint | an endpoint to make a request to |
| x-api-key | this key will be used to identify a company, one key per one company |
| eko-api-key | used to identify a bot user, one key per one bot, one company can have many bots |

These parameters can be asked for from Eko support.

### Content
* [API Usage](#api-usage)
* User Routes
  1. [GET /v0/users](#1-get-users) - get users
* Workspace Routes
  1. [GET /v0/workspaces](#1-get-workspaces) - get workspaces
  2. [GET /v0/workspaces/:id](#2-get-workspace-by-id) - get workspace by id
  3. [POST /v0/workspaces](#3-create-workspace) - create workspace
  4. [PUT /v0/workspaces](#4-update-workspace) - update workspace
  5. [POST /v0/workspaces/add_users](#5-add-users-to-workspace) - add users to workspace
  6. [POST /v0/workspaces/remove_users](#6-remove-users-from-workspace) - remove users from workspace
  7. [POST /v0/workspaces/leave](#7-remove-all-users-from-workspace) - remove every users from workspace
* Topic Routes
  1. [GET /v0/topics](#1-get-topics) - get topics
  2. [POST /v0/topics](#2-create-topic) - create topic
  3. [PUT /v0/topics](#3-update-topic) - update topic
* Message Routes
  1. [POST /v0/messages](#1-send-message) - send a message
* [FAQ](#faq)

### API Usage
#### Request Header

| Header       | Value            |
| ------------ | ---------------- |
| Content-Type | application/json |
| eko-api-key  | *eko-api-key*    |
| x-api-key    | *x-api-key*      |

In case of /v0/messages route, user Content-Type as `multipart/form-data` instead.

#### Error Handling
Along with the HTTP methods that the API responds to, it will also return standard HTTP statuses, including error codes.

```http
HTTP/1.1 403 Forbidden
{
  "id":       "forbidden",
  "message":  "You do not have access for the attempted action.",
  "error_code": 403
}
```

### User Routes

  #### 1. Get users
  ###### GET /v0/users
  Get all users from the network. By default, 25 users will be return.

  | Request Parameters | Required | Default | Description |
  | ------------------ | -------- | ------- | ----------- |
  | skip               | no       | 0       | start index of user pagination |
  | limit              | no       | 25      | limit for pagination (max: 50) |
  | search             | no       |         | a string to search for users by firstname, lastname (max: 10, min: 2 chars) |
  | username**         | no       |         | find by username (exact match) |

  ** username parameter is supported on 9.3.0 ^

  **Curl example**
  ```bash
  # Get users
  curl -X GET \
    https://eko-api.com/v0/users \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'

  # Get 50 users with 50 offset
  curl -X GET \
    https://eko-api.com/v0/users?skip=50&limit=50 \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'

  # Get users with search string
  curl -X GET \
    https://eko-api.com/v0/users?search=john \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'

  # Get a user by username
  curl -X GET \
    https://eko-api.com/v0/users?username=john.doe \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'
  ```

  **Response sample**
  ```json5
  [
    {
      "user_id": "57f2020d9b4dc14db42a1fc0",
      "username": "john.app",
      "firstname": "John",
      "lastname": "Appleseed"
    },
    {
      "user_id": "57f2020d9b4dc14db42a1fc2",
      "username": "john.coo",
      "firstname": "John",
      "lastname": "Cooper"
    }
  ]
  ```

### Workspace Routes

  #### 1. Get workspaces
  ###### GET /workspaces
  Get all workspaces, which are created from this API, from the network. By default, 25 users will be return. The `user_count` attribute identify the number of users in a workspace which includes the bot itself.

  | Request Parameters | Required | Default | Description |
  | ------------------ | -------- | ------- | ----------- |
  | skip               | no       | 0       | start index of workspace pagination |
  | limit              | no       | 25      | limit for pagination (max: 50) |

  **Curl example**
  ```bash
  # Get workspaces
  curl -X GET \
    https://eko-api.com/v0/workspaces \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'

  # Get 50 workwpaces with 50 offset
  curl -X GET \
    https://eko-api.com/v0/workspaces?skip=50&limit=50 \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'
  ```

  **Response sample**
  ```json5
  [
    {
      workspace_id: "57f2020d9b4dc14db42a1fc0",
      name: "IT department workspace",
      user_count: 4
    },
    {
      workspace_id: "57f2020d9b4dc14db42a1fc2",
      name: "Engineering department workspace",
      user_count: 5
    }
  ]
  ```

  #### 2. Get workspace by id
  ###### GET /v0/workspaces/:id
  Get a workspace by id.

  | Request Parameters | Required | Default | Description |
  | ------------------ | -------- | ------- | ----------- |
  | user_skip          | no       | 0       | start index of `users` pagination |
  | user_limit         | no       | 25      | limit of `users` pagination (max: 50) |

  **Curl example**
  ```bash
  # Get a workspace
  curl -X GET \
    https://eko-api.com/v0/workspaces/57f2020d9b4dc14db42a1fc0 \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'

  # Get a workspace with 50 users, 50 offset
  curl -X GET \
    https://eko-api.com/v0/workspaces/57f2020d9b4dc14db42a1fc0?user_skip=50&user_limit=50 \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc0",
    name: "IT department workspace",
    user_counts: 2,
    users: [ {
      user_id: "57f2020d9b4dc14db42a1fc0",
      username: "john",
      firstname: "John",
      lastname: "Kennedy"
    }, {
      user_id: "57f2020d9b4dc14db42a1fc0",
      username: "Alex",
      firstname: "Alex",
      lastname: "Smith"
    }]
  }
  ```


  #### 3. Create workspace
  ###### POST /v0/workspaces
  Create a workspace.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | name         | yes      |         | workspace name |
  | user_ids     | yes      |         | an array of user ids, can be get from GET /v0/users |

  **Curl example**
  ```bash
  curl -X POST \
    https://eko-api.com/v0/workspaces \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "name": "IT department workspace",
    "user_ids": [
      "5a73eecd5090ab0001d6ba44",
      "5a73eecd5090ab0001d6ba22",
      "5a73eecd5090ab0001d6ba11"
    ]
  }'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc5"
    name: "IT department workspace"
    user_count: 4
  }
  ```

  #### 4. Update workspace
  ###### PUT /v0/workspaces
  Update a workspace by given workspace id.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | name         | yes      |         | workspace name |
  | workspace_id | yes      |         | id of the workspace that is needed to be updated |

  **Curl example**
  ```bash
  curl -X PUT \
    https://eko-api.com/v0/workspaces \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "workspace_id": "57f2020d9b4dc14db42a1fc5",
    "name": "IT department workspace 2018"
  }'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc5",
    name: "IT department workspace 2018"
  }
  ```

  #### 5. Add users to workspace
  ###### POST /v0/workspaces/add_users
  Add users to a workspace by given workspace id.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | workspace_id | yes      |         | id of the workspace |
  | user_ids     | yes      |         | an array of user ids, can be get from GET /v0/users |

  **Curl example**
  ```bash
  curl -X POST \
    https://eko-api.com/v0/workspaces/add_users \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "workspace_id": "57f2020d9b4dc14db42a1fc5",
    "user_ids": [
      "5938d9e7a0ca250001cb66b0"
    ]
  }'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc5"
    name: "IT department workspace"
    user_count: 5
  }
  ```


  #### 6. Remove users from workspace
  ###### POST /v0/workspaces/remove_users
  Remove users from a workspace by given workspace id.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | workspace_id | yes      |         | id of the workspace |
  | user_ids     | yes      |         | an array of user ids, can be get from GET /v0/users |

  **Curl example**
  ```bash
  curl -X POST \
    https://eko-api.com/v0/workspaces/remove_users \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "workspace_id": "57f2020d9b4dc14db42a1fc5",
    "user_ids": [
      "5938d9e7a0ca250001cb66b0"
    ]
  }'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc5"
    name: "IT department workspace"
    user_count: 4
  }
  ```


  #### 7. Remove all users from workspace
  ###### POST /v0/workspaces/leave
  Remove every users from a workspace by given workspace id.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | workspace_id | yes      |         | id of the workspace |

  **Curl example**
  ```bash
  curl -X POST \
    https://eko-api.com/v0/workspaces/leave \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "workspace_id": "57f2020d9b4dc14db42a1fc5"
  }'
  ```

  **Response sample**
  HTTP 202 with empty body

### Topic Routes

  #### 1. Get topics
  ###### GET /v0/topics
  Get all topics from a given workspace id. By default, 25 topics will be return.

  | Request Parameters | Required | Default | Description |
  | ------------------ | -------- | ------- | ----------- |
  | workspace_id       | yes      |         | id of a workspace |
  | skip               | no       | 0       | start index of user pagination |
  | limit              | no       | 25      | limit for pagination (max: 50) |

  **Curl example**
  ```bash
  # Get all topics from a workspace
  curl -X GET \
    https://eko-api.ekoapp.com/v0/topics?workspace_id=57f2020d9b4dc14db42a1fc0 \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'

  # Get 50 topics with 50 offset
  curl -X GET \
    https://eko-api.ekoapp.com/v0/topics?workspace_id=57f2020d9b4dc14db42a1fc0&skip=50&limit=50 \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad'
  ```

  **Response sample**
  ```json5
  [
    {
      topic_id: "57f2020d9b4dc14db42a1fc5",
      name: "Annual report",
      read_by_count: 20
    },
    {
      topic_id: "57f2020d9b4dc14db42a1fc6",
      name: "Weekly report",
      read_by_count: 4
    }
  ]
  ```

  #### 2. Create topic
  ###### POST /v0/topics
  Create a topic to given workspace id.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | workspace_id | yes      |         | id of a workspace |
  | name         | yes      |         | name of a topic |

  **Curl example**
  ```bash
  curl -X POST \
    https://eko-api.ekoapp.com/v0/topics \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "name": "Monthly report topic",
    "workspace_id": "57f2020d9b4dc14db42a1fc0"
  }'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc0",
    topic_id: "57f2020d9b4dc14db42a1fc1",
    name: "Monthly report topic"
  }
  ```

  #### 3. Update topic
  ###### PUT /v0/topics
  Update a topic by given topic id.

  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | topic_id     | yes      |         | id of a topic |
  | workspace_id | yes      |         | id of a workspace |
  | name         | yes      |         | name of a topic |

  **Curl example**
  ```bash
  curl -X PUT \
    https://eko-api.ekoapp.com/v0/topics \
    -H 'content-type: application/json' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a551a67' \
    -H 'x-api-key: ajxPs55mrL3WNavrp7cuq9m563rT671nalruscad' \
    -d '{
    "name": "Monthly report 2018",
    "workspace_id": "57f2020d9b4dc14db42a1fc0",
    "topic_id": "57f2020d9b4dc14db42a1fc1"
  }'
  ```

  **Response sample**
  ```json5
  {
    workspace_id: "57f2020d9b4dc14db42a1fc0",
    topic_id: "57f2020d9b4dc14db42a1fc1",
    name: "Monthly report 2018"
  }
  ```

### Message Routes

  #### 1. Send message
  ###### GET /v0/messages
  Send a text message or file to a workspace or a user private chat.
  - To send the content as a text message, specify `message` as a parameter.
  - To send the content as a file, specify `file` as a parameter.
  * To send the content to a workspace, specify `workspace_id` and `topic_id` as a parameter.
  * To send the content to a user privae chat, specify `user_id` as a parameter.


  | Request Body | Required | Default | Description |
  | ------------ | -------- | ------- | ----------- |
  | workspace_id | required when sending content to workspace | | id of workspace to sent message to |
  | topic_id     | required when sending content to workspace | | id of topic to sent message to |
  | user_id      | required when sending content to user      | | id of user to sent message to |
  | file         | required when sending text message         | | text to be sent |
  | message      | required when sending file                 | | file to be sent |

  **Curl example**
  ```bash
  # Send text message to a workspace
  curl -X POST \
    https://eko-api.com/messages \
    -H 'content-type: multipart/form-data' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a59842c' \
    -H 'x-api-key: NYHvsNWbFB6fQ7C0nP0TV2H7xpvVTcMvaHeYspwQ' \
    -F 'workspace_id=57f2020d9b4dc14db42a1fc0' \
    -F 'topic_id=57f2020d9b4dc14db42a1fc1' \
    -F 'message_data=Hello Eko!'

  # Send text message to a user
  curl -X POST \
    https://eko-api.com/messages \
    -H 'content-type: multipart/form-data' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a59842c' \
    -H 'x-api-key: NYHvsNWbFB6fQ7C0nP0TV2H7xpvVTcMvaHeYspwQ' \
    -F 'user_id=57f2020d9b4dc14db42a1fc2' \
    -F 'message_data=Hello Eko!'

  # Send file message to a workspace
  curl -X POST \
    https://eko-api.com/messages \
    -H 'content-type: multipart/form-data' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a59842c' \
    -H 'x-api-key: NYHvsNWbFB6fQ7C0nP0TV2H7xpvVTcMvaHeYspwQ' \
    -F 'workspace_id=57f2020d9b4dc14db42a1fc0' \
    -F 'topic_id=57f2020d9b4dc14db42a1fc1' \
    -F 'file=@test.jpg; type=image/jpeg'

  # Send file message to a user
  curl -X POST \
    https://eko-api.com/messages \
    -H 'content-type: multipart/form-data' \
    -H 'eko-api-key: 763de5858b2414750bf947fb1a59842c' \
    -H 'x-api-key: NYHvsNWbFB6fQ7C0nP0TV2H7xpvVTcMvaHeYspwQ' \
    -F 'user_id=57f2020d9b4dc14db42a1fc2' \
    -F 'file=@test.jpg; type=image/jpeg'
  ```

  **Response sample**
  ```json5
  {
    message_data: "Hello Eko!",
    message_id: "57f2020d9b4dc14db42a1fc0",
    message_type: "text"
  }
  ```

  **File Type Support**
  ```
  # MIME type
  'jpg,jpeg' => 'image/jpeg'
  'png' => 'image/png'
  'mp4' => 'image/mp4'
  'pdf' => 'application/pdf'
  'zip' => 'application/zip'

  'docx' => 'application/vnd.openxmlformats-officedocument.wordprocessingml.document'
  'doc' => 'application/msword'
  'pptx' => 'application/vnd.openxmlformats-officedocument.presentationml.presentation'
  'ppt' => 'application/vnd.ms-powerpoint'
  'xlsx' => 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
  'xls' => 'application/vnd.ms-excel'
  ```

###FAQ

1. Why can't I curl `POST /v0/messages` message with special characters (ie. กขค) ? <br>
    **->>>** If you are using cUrl client on windows, cUrl has an issue on [handling Unicode arguments in Windows](https://curl.haxx.se/docs/knownbugs.html#can_t_handle_Unicode_arguments_i). <br><br>
2. How to send a message with new line character? <br>
    **->>>** Use `\n`. If you are using curl client and trying to send `\n` as parameter, curl will try to convert `\n` to \n string. To solve this, please refer to this [link](https://stackoverflow.com/questions/3872427/how-to-send-line-break-with-curl). <br><br>
3. I need more bot accounts (more api keys). How can I achieve this? <br>
    **->>>** Please contact Eko via Eko Support account of your network. <br><br>
4. Can I change the name of the bot? <br>
    **->>>** Yes you can, by logging in as a bot an edit its profile. If you do not have the bot password, contact Eko Support. <br><br>
5. I created a workspace from the web, but wehen I try `GET /v0/workspaces`, I cannot see the workspace I recently created. <br>
    **->>>** `GET /v0/workspaces` will only fetch the workspace created by API. <br><br>



