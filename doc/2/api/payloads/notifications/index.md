---
code: false
type: page
title: Notifications
description: Notifications payloads reference
order: 300
---

# Notifications

## Document Notification

The following notifications are sent by Kuzzle whenever one of the following event matches the subscription filters:

- A real-time message is sent
- A new document has been created
- A document has been updated or replaced

Real-time notifications are also sent when documents, previously in the subscription scope, are leaving it because of the following events:

- document is deleted
- document is updated/replaced and its new content do not match the subscription filters anymore

### Format

A document notification contains the following fields:

| Property     | Type   | Description                                                                                           |
|--------------|--------|-------------------------------------------------------------------------------------------------------|
| `action`     | string | API controller's action                                                                               |
| `collection` | string | Collection name                                                                                       |
| `controller` | string | API controller                                                                                        |
| `event`      | string | Event type (`write`, `delete` or `publish`)                                                           |
| `index`      | string | Index name                                                                                            |
| `node`       | string | Unique identifier of the node who generated the notification                                          |
| `protocol`   | string | Network protocol used to modify the document                                                          |
| `result`     | object | Notification content                                                                                  |
| `room`       | string | Subscription channel identifier. Can be used to link a notification to its corresponding subscription |
| `scope`      | string | `in`: document enters (or stays) in the scope<br/>`out`: document leaves the scope                    |
| `timestamp`  | number | Timestamp of the event, in Epoch-millis format                                                        |
| `type`       | string | `document`: the notification type                                                                     |
| `volatile`   | object | KuzzleRequest [volatile data](/core/2/guides/main-concepts/api#volatile-data)                         |

The `result` object is the notification content, and it has the following structure:

| Property         | Type     | Description                                                                        |
|------------------|----------|------------------------------------------------------------------------------------|
| `_id`            | string   | Document unique ID<br/>`null` if the notification is from a real-time message      |
| `_source`        | object   | The message or full document content.                                              |
| `_updatedFields` | string[] | List of fields that have been updated (only available on document partial updates) |

### Example

```js
{
  "index": "foo",
  "collection": "bar",
  "controller": "document",
  "action": "create",
  "protocol": "http",
  "timestamp": 1497513122738,
  "volatile": null,
  "scope": "in",
  "node": "knode-nasty-author-4242",
  "result":{
    "_source":{
      "some": "document content",
      "_kuzzle_info": {
        "author": "<author kuid>",
        "createdAt": 1497866996975
      }
    },
    "_id": "<document identifier>"
  },
  "room":"893e183fc7acceb5-7a90af8c8bdaac1b"
}
```

---

## User Notification

User notifications are triggered by the following events:

- A user subscribes to the same room
- A user leaves that room

These notifications are sent only if the `users` argument is set to any other value than the default `none` one (see [subscription request](/core/2/api/controllers/realtime/subscribe)).

### Format

| Property     | Type   | Description                                                                                           |
|--------------|--------|-------------------------------------------------------------------------------------------------------|
| `action`     | string | API controller's action                                                                               |
| `collection` | string | Data collection                                                                                       |
| `controller` | string | API controller                                                                                        |
| `index`      | string | Data index                                                                                            |
| `node`       | string | Unique identifier of the node who generated the notification                                          |
| `protocol`   | string | Network protocol used by the entering/leaving user                                                    |
| `result`     | object | Notification content                                                                                  |
| `room`       | string | Subscription channel identifier. Can be used to link a notification to its corresponding subscription |
| `timestamp`  | number | Timestamp of the event, in Epoch-millis format                                                        |
| `type`       | string | `user`: the notification type                                                                         |
| `user`       | string | `in`: a new user has subscribed to the same filters<br/>`out`: a user cancelled a shared subscription |
| `volatile`   | object | KuzzleRequest [volatile data](/core/2/guides/main-concepts/api#volatile-data)                         |

The `result` object is the notification content, and it has the following structure:

| Property | Type   | Description                                        |
|----------|--------|----------------------------------------------------|
| `count`  | number | Updated users count sharing that same subscription |

### Example

```js
{
  "index": "<index name>",
  "collection": "<collection name>",
  "controller": "realtime",
  "action": "subscribe",
  "protocol": "websocket",
  "timestamp": 1497517009931,
  "user": "in",
  "node": "knode-nasty-author-4242",
  "result": {
    "count": 42
  },
  "volatile": {
    "fullname": "John Snow",
    "favourite season": "winter",
    "goal in life": "knowing something"
  }
}
```

---

## Server Notification

Server notifications are triggered by global events, and they are sent to all of a client's subscriptions at the same time.

Currently, the only event generating a server notification is when an [authentication token](/core/2/guides/main-concepts/authentication#authentication-token) has expired, closing the subscription.

Other events may be added in the future.

### Format

| Property  | Type   | Value                                                              |
|-----------|--------|--------------------------------------------------------------------|
| `message` | string | Server message explaining why this notification has been triggered |
| `type`    | string | `TokenExpired`: notification type                                  |

### Example

```js
{
  "message": "Authentication Token Expired",
  "type": "TokenExpired"
}
```

## Debugger Notification

Debugger notifications are triggered by the [Debug Controller](/core/2/api/controllers/debug) only when the debugger is enabled.
Those notifications are sent to every connections currently listening to events from the [Debug Controller](/core/2/api/controllers/debug) using
the action [debug:addListener](/core/2/api/controllers/debug/add-listener).

### Format

| Property     | Type   | Description                                                                                           |
|--------------|--------|-------------------------------------------------------------------------------------------------------|
| `event`      | string | Name of the event that triggered the notification                                                     |
| `result`     | object | Notification content                                                                                  |
| `room`       | string | Subscription channel identifier. Will always be `kuzzle-debugger-event` |

### Example

```js
{
  "room": "kuzzle-debugger-event",
  "event": "HeapProfiler.reportHeapSnapshotProgress",
  "result": {
      "method": "HeapProfiler.reportHeapSnapshotProgress",
      "params": {
          "done": 238276,
          "total": 238276,
          "finished": true
      }
  }
}
```