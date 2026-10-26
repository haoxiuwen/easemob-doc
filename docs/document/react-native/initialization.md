# 初始化

初始化是使用 SDK 的必要步骤，需在所有接口方法调用前完成。

如果进行多次初始化操作，只有第一次初始化以及相关的参数生效。

:::tip
需要在主进程中进行初始化。
:::

## 前提条件

有效的环信即时通讯 IM 开发者账号和 App key，详见 [环信控制台的相关文档](/product/console/app_create.html)。

## 初始化 SDK

初始化时，你需要通过 `ChatOptions.withAppKey` 创建包含 App Key 的初始化配置。

```typescript
const options = ChatOptions.withAppKey({
  appKey,
});

ChatClient.getInstance()
  .init(options)
  .then(() => {
    console.log("init: success");
  })
  .catch((reason) => {
    console.error(reason);
  });
```

下表列明初始化配置 `ChatOptions` 封装的常用属性，请在初始化 SDK 前完成配置。

| 属性 | 描述 |
| :----------------- | :---------------- |
| `appKey` | 设置 App Key。`appKey` 参数为创建 App 时在环信控制台上注册的 App 唯一识别符。 |
| `autoLogin` | 是否自动登录。<br/> -（默认）`true`：自动登录。**若使用默认设置，首次登录后，后续会自动登录。这种情况下，若再手动登录，则会提示用户已登录。**<br/> - `false`：不自动登录。 |
| `autoAcceptGroupInvitation` | 是否自动接受加群邀请。<br/> -（默认）`false`：不自动接受加群申请；<br/> - `true`：自动接受加群申请。 |
| `acceptInvitationAlways` | 是否自动接受加好友邀请。<br/> -（默认）`false`：不自动接受好友邀请；<br/> - `true`：自动接受好友邀请。 |
| `apnsCertName` | APNs 证书名称，用于 iOS 离线推送。该值应与环信控制台中配置的 APNs 证书名称一致，并且只能在初始化 SDK 时设置。 |
| `pushKitCertName` | PushKit 证书名称，用于 iOS VoIP 推送。该值应与环信控制台中配置的 PushKit 证书名称一致，并且只能在初始化 SDK 时设置。 |
| `deleteMessagesAsExitChatRoom` | 退出（主动和被动退出）聊天室时是否删除聊天消息。<br/> -（默认）`true`：删除；<br/> - `false`：保留。 |
| `deleteMessagesAsExitGroup` | 退出（主动和被动退出）群组时是否删除聊天消息。<br/> -（默认）`true`：退出群组时删除群组消息；<br/> - `false`：退出群组时不删除群组消息。 |
| `isChatRoomOwnerLeaveAllowed` | 是否允许聊天室所有者离开并删除会话记录。<br/> -（默认）`true`：允许；<br/> - `false`：不允许。 |
| `enableChatroomConversation` | 设置获取本地会话列表时是否包含聊天室会话。该配置不控制聊天室会话的创建或存储，也不影响聊天室消息的正常收发。<br/> - `true`：本地会话列表中包含聊天室会话。<br/> -（默认）`false`：本地会话列表中不包含聊天室会话。必须在初始化 SDK 前设置。 |
| `enableEmptyConversation` | 设置获取本地会话时是否包含空会话。<br/> - `true`：包含空会话。<br/> -（默认）`false`：不包含空会话。 |
| `autoLoadConversations` | 设置初始化 SDK 时是否自动将全部本地会话加载到内存。<br/> -（默认）`true`：自动加载全部会话。<br/> - `false`：不自动加载全部会话，可使用 `fetchConversationsFromDB` 分页加载。 |

## 初始化后设置监听

初始化后，你可以设置所需的监听，例如，连接监听和接收消息的监听，及时知晓长连接的建立和消息的收发。

```typescript
ChatClient.getInstance().addConnectionListener({
  // SDK 成功连接到 IM 服务器时触发。
  onConnected(): void {
    console.log('LoginAndLogoutScreen.onConnected');
  },
  // SDK 与 IM 服务器断开连接时触发。
  onDisconnected(): void {
    console.log('LoginAndLogoutScreen.onDisconnected');
  },
});
ChatClient.getInstance().chatManager.addMessageListener({
   // 收到消息时触发。
   onMessagesReceived(messages: Array<ChatMessage>): void {
    console.log('onMessagesReceived: ', messages);
  },
});
```

## 设置登录后自动同步数据

### 同步的数据

SDK 支持在初始化前通过 `ChatOptions#dataSyncType` 配置登录后自动同步的数据类型。用户登录成功后，SDK 按配置同步服务端数据并更新本地缓存或数据库。

当前支持同步会话列表、好友列表以及当前用户已加入的群组列表。各数据类型的配置项、本地读取方式如下：

| 配置项                           | 登录后自动同步内容       | 本地读取方式                              | 说明                                                         |
| -------------------------------- | ------------------------ | ----------------------------------------- | ------------------------------------------------------------ |
| `ChatDataSyncType.Conversations` | 会话列表                 | `ChatManager#getAllConversations`         | 从本地读取会话列表，返回 `Promise<ChatConversation[]>`，不会主动向服务器请求会话列表。 |
| `ChatDataSyncType.Contacts`      | 好友列表                 | `ChatContactManager#getAllContactsFromDB` | 从本地数据库读取好友 ID 列表，返回 `Promise<string[]>`。如需读取好友对象，可调用 `ChatContactManager#getAllContacts`。 |
| `ChatDataSyncType.JoinedGroups`  | 当前用户已加入的群组列表 | `ChatGroupManager#getJoinedGroups`        | 从 SDK 本地数据中读取已加入的群组列表，返回 `Promise<ChatGroup[]>`。 |

如需检查初始化时配置的自动同步数据类型，可读取 `ChatClient.getInstance().options?.dataSyncType`。该属性仅用于读取配置，不会触发数据同步；未显式配置时，其值为 `undefined`，不表示不自动同步。

登录成功不代表数据已同步完成。如需读取同步后的数据，应通过 `ChatConnectEventListener#onDataSyncFinish` 确认对应类型同步成功，回调中的 `errorCode` 为 `0` 表示成功。

### 配置方式

必须在调用 `ChatClient#init` 前设置 `ChatOptions.dataSyncType`。SDK 初始化完成后再修改原配置对象，不会应用于本次已创建的 SDK 实例。

配置规则如下：

- React Native SDK 5.0.0 未设置 `dataSyncType` 时，沿用原生 SDK 默认值：Android 端默认不自动同步，iOS 端默认同步会话列表。为保证双端行为一致，建议显式配置。
- 需要同步一种数据时，传入对应的 `ChatDataSyncType` 枚举值。
- 需要同步多种数据时，通过按位或运算符 `|` 组合对应的枚举值。
- 设置为 `ChatDataSyncType.None` 表示不自动同步。

以下示例表示登录成功后自动同步会话列表、好友列表和当前用户已加入的群组列表：

```java
async function initializeSdk(): Promise<void> {
  const options = ChatOptions.withAppKey({
    appKey: '<org_name>#<app_name>',
    // 登录后自动同步会话、好友和已加入的群组
    dataSyncType:
      ChatDataSyncType.Conversations |
      ChatDataSyncType.Contacts |
      ChatDataSyncType.JoinedGroups,
  });

  try {
    // 使用指定配置初始化 SDK
    await ChatClient.getInstance().init(options);
  } catch (error: unknown) {
    console.error('初始化 SDK 失败：', error);
  }
}
```

如果只需要同步会话列表，可在初始化前仅配置 `Conversations`：

```java
options.dataSyncType = ChatDataSyncType.Conversations;
```

如果需要关闭登录后的自动同步，可在初始化前配置 `None`：

```java
options.dataSyncType = ChatDataSyncType.None;
```

## 开启用户信息自动管理

React Native SDK 支持通过 `ChatOptions#enableUserInfo` 配置用户信息自动管理功能。该参数默认为 `false`；设置为 `true` 后，SDK 会在用户登录成功时自动同步当前登录用户的用户属性。其他用户的用户属性会在接收相关消息、主动从服务端获取用户属性等场景下更新。

`enableUserInfo` 与 `enableAutoSyncContacts` 相互独立，开启用户信息自动管理不会在登录时自动获取所有好友的昵称和头像。用户属性更新事件、本地读取与主动获取方式，详见 [用户信息自动管理](userinfo_provider.html)。

如果两项功能都需要，可在同一次初始化中配置：

```typescript
const options = ChatOptions.withAppKey({
  appKey: 'your-org#your-app',
  enableAutoSyncContacts: true,
  enableUserInfo: true,
});

await ChatClient.getInstance().init(options);
```

## 接口列表

| API 名称 | 所属模块/类 | 返回类型 | 说明 |
| :--- | :--- | :--- | :--- |
| [`withAppKey`](#初始化-sdk) / [`withAppId`](#初始化-sdk) | `ChatOptions` | `ChatOptions` | 使用 App Key 或 App ID 创建初始化配置。 |
| [`init`](#初始化-sdk) | `ChatClient` | `Promise<void>` | 使用指定配置初始化 React Native SDK。 |
| [`apnsCertName`](/document/react-native/push/push_easemob_console.html#配置-ios-推送证书名称) | `ChatOptions` | `string \| undefined` | 设置 iOS APNs 证书名称。 |
| [`pushKitCertName`](/document/react-native/push_easemob_console.html#配置-ios-推送证书名称) | `ChatOptions` | `string \| undefined` | 设置 iOS PushKit 证书名称。 |
| [`enableChatroomConversation`](conversation_list.html#从本地获取会话列表) | `ChatOptions` | `boolean` | 设置获取本地会话列表时是否包含聊天室会话。 |
| [`enableEmptyConversation`](conversation_list.html#从本地获取会话列表) | `ChatOptions` | `boolean` | 设置获取本地会话时是否包含空会话。 |
| [`autoLoadConversations`](conversation_list.html#分页获取本地会话) | `ChatOptions` | `boolean` | 设置初始化 SDK 时是否自动将全部本地会话加载到内存。 |
| [`enableAutoSyncContacts`](#设置登录后自动同步好友数据) | `ChatOptions` | `boolean` | 登录后自动同步好友数据的初始化配置。 |
| [`enableUserInfo`](#开启用户信息自动管理) | `ChatOptions` | `boolean` | 用户信息自动管理的初始化配置。 |
| [`addConnectionListener`](#初始化后设置监听) | `ChatClient` | `void` | 添加连接状态监听器。 |
| [`addMessageListener`](#初始化后设置监听) | `ChatManager` | `void` | 添加消息相关监听器。 |
