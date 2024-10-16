# 设置推送扩展功能

你可以利用扩展字段实现自定义推送设置，通知栏折叠、强制推送和发送静默消息。

## 设置自定义推送字段

创建推送消息时，可以在消息中添加自定义字段，满足个性化业务需求。

```typescript
msg.attributes = {
  // 消息扩展字段。
   "em_push_ext": {
        "custom": {
            "key1": "value1",
            "key2": "value2"
        }
    },
};
```

自定义字段的数据结构如下：

```json
{
    "em_push_ext": {
        "custom": {
            "key1": "value1",
            "key2": "value2"
        }
    }
}
```

| 参数             | 描述               |
| :--------------- | :----------------- |
| `em_push_ext`    | 消息推送扩展固定值，不可修改。 |
| `custom`         | 消息扩展，使用扩展的方式向推送中添加自定义字段，该值为固定值。 |
| `key1`/`key2`    | 自定义消息推送扩展的具体内容。 |

## 设置某些群成员收到推送通知

在离线推送免打扰模式下，若你在群组中发送消息时只希望某些群成员收到离线推送通知，可通过设置消息扩展字段实现。

```typescript
msg.attributes = {
  em_apns_ext: {
     "em_at_list": "All"
    },
};
```

该扩展字段的数据结构如下：

```json
{
    "em_apns_ext": {
        "em_at_list": "All"
    }
}
```

| 参数             | 描述               |
| :--------------- | :----------------- |
| `em_apns_ext`    | 内置的消息扩展字段。 |
| `em_at_list`          | 内置的扩展字段 key，对应的 value 为数组类型，表示接收推送通知的群成员的用户 ID，设置为 `All` 表示向所有群成员推送通知。  |

## 设置通知栏折叠

你可以将通知栏中的多条消息折叠起来，示例代码如下：

```typescript
msg.attributes = {
  em_apns_ext: {
    em_push_collapse_key: "collapseKey", 
  },
};
```

通知栏折叠字段的数据结构如下：

```json
{
    "em_apns_ext": {
        "em_push_collapse_key": "collapseKey"
    }
}
```

| 参数             | 描述               |
| :--------------- | :----------------- |
| `em_apns_ext`    | 内置的消息扩展字段。  |
| `em_push_collapse_key`   | 指定一组可折叠的消息（例如，含有 collapse_key: “Updates Available”），以便恢复传送时只发送最后一条消息，从而避免设备恢复在线状态或变为活跃状态时重复发送过多相同的消息。   |

## 强制推送

设置强制推送后，用户发送消息时会忽略接收方的免打扰设置，不论是否处于免打扰时间段都会正常向接收方推送消息。

```typescript
// 下面以文本消息为例，其他类型的消息设置方法相同。
msg.attributes = {
  // 是否为强制推送。该字段为内置字段，取值如下：`true`：强制推送；（默认）`false`：非强制推送。
  em_force_notification: true,
};
```

## 发送静默消息

发送静默消息指发送方在发送消息时设置不推送消息，即用户离线时，环信即时通讯 IM 服务不会通过第三方厂商的消息推送服务向该用户的设备推送消息通知。因此，用户不会收到消息推送通知。当用户再次上线时，会收到离线期间的所有消息。

发送静默消息和免打扰模式下均为不推送消息，区别在于发送静默消息为发送方在发送消息时设置，而免打扰模式为接收方设置在指定时间段内不接收推送通知。

```typescript
// 下面以文本消息为例，其他类型的消息设置方法相同。
msg.attributes = {
  // 是否发送静默消息。该字段为内置字段，取值如下：`true`：发送静默消息；（默认）`false`：推送该消息。
  em_ignore_notification: true,
};
```