# 发送和接收消息

<Toc />

登录即时通讯服务后，用户可以在单聊、群聊、聊天室中发送如下类型的消息：

- 文字消息，包含超链接和表情；
- 附件消息，包含图片、语音、视频及文件消息；
- 位置消息；
- 透传消息；
- 自定义消息；
- 合并消息。

对于单聊，环信即时通信 IM 默认支持陌生人之间发送消息，即无需添加好友即可聊天。若仅允许好友之间发送单聊消息，你需要你需要[开启好友关系检查](/product/enable_and_configure_IM.html#好友关系检查)。对于群组和聊天室，用户每次只能向所属的单个群组和聊天室发送消息。

针对聊天室消息并发量较大的场景，即时通讯服务提供消息分级功能。你可以通过设置消息优先级，将消息划分为高、普通和低三种级别。你可以在创建消息时，将指定消息类型，或指定成员的所有消息设置为高优先级，确保此类消息优先送达。这种方式可以确保在聊天室内消息并发量较大或消息发送频率过高的情况下，服务器首先丢弃低优先级消息，将资源留给高优先级消息，确保重要消息（如打赏、公告等）优先送达，以此提升重要消息的可靠性。请注意，该功能并不保证高优先级消息必达。在聊天室内消息并发量过大的情况下，为保证用户实时互动的流畅性，即使是高优先级消息仍然会被丢弃。

本文介绍如何使用即时通讯 IM SDK 实现小程序发送和接收这些类型的消息。

## 技术原理

环信即时通讯 IM SDK 可以实现消息的发送和接收。

发送和接收消息：

- 消息发送方调用 `create` 方法创建文本、文件或附件消息。
- 消息发送方调用 `send` 方法发送消息。
- 消息接收方调用 `addEventHandler` 监听消息事件，并在相应回调中接收消息。

消息收发流程如下：

1. 用户 A 发送一条消息到环信的消息服务器。
2. 单聊时消息服务器发消息给用户 B，群聊时发消息给群内其他每个成员。
3. 用户收到消息。

![img](/images/web/sendandreceivemsg.png)

## 前提条件

开始前，请确保满足以下条件：

- 完成 SDK 初始化，详见 [快速开始](wechat.html)。
- 了解环信即时通讯 IM 的使用限制，详见 [使用限制](/product/limitation.html)。

## 实现方法

### 发送文本消息

使用 `Message` 类创建并发送文本消息。

默认情况下，SDK 对单个用户发送消息的频率未做限制。如果你联系了环信商务设置了该限制，一旦在单聊、群聊或聊天室中单个用户的消息发送频率超过设定的上限，SDK 会上报错误，即错误码 509 `MESSAGE_CURRENT_LIMITING`。

示例代码如下：

```javascript
// 发送文本消息。
function sendTextMessage() {
  let option = {
    // 消息类型。
    type: "txt",
    // 消息内容。
    msg: "message content",
    // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
    to: "username",
    // 会话类型：单聊、群聊和聊天室分别为 `singleChat`、`groupChat` 和 `chatRoom`，默认为单聊。
    chatType: "singleChat",
  };
  // 创建文本消息。
  let msg = WebIM.message.create(option);
  // 调用 `send` 方法发送该文本消息。
  conn.send(msg).then((res)=>{
     console.log("Send message success",res);
  }).catch((e)=>{
      console.log("Send message fail",e);
  });
}
```

对于聊天室消息，可设置消息优先级。示例代码如下：

```javascript
// 发送文本消息。
function sendTextMessage() {
    let option = {
        type: "txt",
        msg: "message content",
        // 聊天室消息的优先级。如果不设置，默认值为 `normal`，即“普通”优先级。
        priority: "high"
        to: "chat room ID",
        chatType: "chatRoom",
    };
    let msg = WebIM.message.create(opt);
    conn.send(msg).then(()=>{
        console.log("Send message success");
    }).catch((e)=>{
        console.log("Send message fail");
    });
}
```

若初始化时打开了 `useReplacedMessageContents` 开关，发送文本消息时如果被内容审核（Moderation）进行了内容替换，发送方会收到替换后的内容。若该开关为关闭状态，则发送方不会收到替换后的内容。

### 接收消息

你可以通过 `addEventHandler` 注册监听器监听消息事件。你可以添加多个事件。当不再监听事件时，请确保删除监听器。

当消息到达时，接收方会收到 `onXXXMessage` 回调。每个回调包含一条或多条消息。你可以遍历消息列表，并可以解析和展示回调中的消息。

```javascript
// 使用 `addEventHandler` 监听回调事件
WebIM.conn.addEventHandler("eventName", {
  // SDK 与环信服务器连接成功。
  onConnected: function (message) {},
  // SDK 与环信服务器断开连接。
  onDisconnected: function (message) {},
  // 当前用户收到文本消息。
  onTextMessage: function (message) {},
  // 当前用户收到图片消息。
  onImageMessage: function (message) {},
  // 当前用户收到透传消息。
  onCmdMessage: function (message) {},
  // 当前用户收到语音消息。
  onAudioMessage: function (message) {},
  // 当前用户收到位置消息。
  onLocationMessage: function (message) {},
  // 当前用户收到文件消息。
  onFileMessage: function (message) {},
  // 当前用户收到自定义消息。
  onCustomMessage: function (message) {},
  // 当前用户收到视频消息。
  onVideoMessage: function (message) {},
  // 当前用户订阅的其他用户的在线状态更新。
  onPresence: function (message) {},
  // 当前用户收到好友邀请。
  onContactInvited: function (msg) {},
  // 联系人被删除。
  onContactDeleted: function (msg) {},
  // 新增联系人。
  onContactAdded: function (msg) {},
  // 当前用户发送的好友请求被拒绝。
  onContactRefuse: function (msg) {},
  // 当前用户发送的好友请求被同意。
  onContactAgreed: function (msg) {},
  // 当前用户收到群组邀请。
  onGroupEvent: function (message) {},
  // 本机网络连接成功。
  onOnline: function () {},
  // 本机网络掉线。
  onOffline: function () {},
  // 调用过程中出现错误。
  onError: function (message) {},
  // 当前用户收到的消息被消息发送方撤回。
  onRecallMessage: function (message) {},
  // 当前用户发送的消息被接收方收到。
  onReceivedMessage: function (message) {},
  // 当前用户收到消息送达回执。
  onDeliveredMessage: function (message) {},
  // 当前用户收到消息已读回执。
  onReadMessage: function (message) {},
  // 当前用户收到会话已读回执。
  onChannelMessage: function (message) {},
});
```

### 发送附件消息

语音、图片、视频和文件消息本质上是附件消息。发送和接收附件消息的流程如下：

1. 创建和发送附件类型消息。SDK 将附件上传到环信服务器，获取消息的基本信息以及服务器上附件文件的路径。

   对于图片消息来说，服务器会自动生成图片的缩略图；而对于视频消息来说，视频的首帧为缩略图。

2. 接收附件消息。

   接收方可以自行下载语音、图片、图片缩略图、视频和文件。

#### 发送语音消息

在发送语音消息前，你应该在 app 级别实现录音，提供录制的语音文件的 URI 和时长（单位为秒）。

参考以下代码示例创建和发送语音消息：

```javascript
/**
 * @param {Object} tempFilePath - 要上传的文件的小程序临时文件路径。
 * @param {Object} duration - 语音时长，单位为秒。
 */
function sendPrivateAudio(tempFilePath, duration) {
  var str = WebIM.config.appkey.split("#");
  var token = WebIM.conn.context.accessToken;
  var domain = WebIM.conn.apiUrl;
  wx.uploadFile({
    url: domain + "/" + str[0] + "/" + str[1] + "/chatfiles",
    filePath: tempFilePath,
    name: "file",
    header: {
      Authorization: "Bearer " + token
    },
    success(res) {
      var dataObj = JSON.parse(res.data);
      var option = {
        type: "audio",
        chatType: "singleChat",
        filename: tempFilePath,
        // 消息接收方：单聊为对端用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
        to: "username", 
        body: {
          //文件 URL。
          url: dataObj.uri + "/" + dataObj.entities[0].uuid,
          //文件类型。
          type: "audio",
          //文件名。
          filename: tempFilePath,
          // 音频文件时长，单位为秒。
          length: Math.ceil(duration / 1000),
        },
      };
      let msg = WebIM.message.create(option);
      // 调用 `send` 方法发送该语音消息。
      WebIM.conn
        .send(msg)
        .then((res) => {
          // 语音消息成功发送。
          console.log("Success");
        })
        .catch((e) => {
          // 语音消息发送失败。
          console.log("Fail", e);
        });
    },
  });
}
```

#### 发送图片消息

参考以下代码示例创建和发送图片消息：

```javascript
function sendImage() {
  var me = this;
  wx.chooseImage({
    count: 1,
    sizeType: ["original", "compressed"],
    sourceType: ["album"],
    success(res) {
      me.sendPrivateImg(res);
    },
  });
}

function sendPrivateImg(res) {
  var me = this;
  var tempFilePaths = res.tempFilePaths;
  var token = WebIM.conn.context.accessToken;
  wx.getImageInfo({
    src: res.tempFilePaths[0],
    success(res) {
      var allowType = {
        jpg: true,
        gif: true,
        png: true,
        bmp: true,
      };
      var str = WebIM.config.appkey.split("#");
      var width = res.width;
      var height = res.height;
      var index = res.path.lastIndexOf(".");
      var filetype = (~index && res.path.slice(index + 1)) || "";
      var domain = wx.WebIM.conn.apiUrl + "/";
      if (filetype.toLowerCase() in allowType) {
        wx.uploadFile({
          url: domain + str[0] + "/" + str[1] + "/chatfiles",
          filePath: tempFilePaths[0],
          name: "file",
          header: {
            Authorization: "Bearer " + token
          },
          success(res) {
            if (res.statusCode === 400) {
              // 图片上传阿里云检验不合法
              var errData = JSON.parse(res.data);
              if (errData.error === "content improper") {
                wx.showToast({
                  title: "图片不合法",
                });
                return false;
              }
            }
            var data = res.data;
            var dataObj = JSON.parse(data);
            var option = {
              type: "img",
              chatType: "singleChat",
              width: width,
              height: height,
              url: dataObj.uri + "/" + dataObj.entities[0].uuid,
              // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
              to: "username", 
            };
            let msg = WebIM.message.create(option);
            // 调用 `send` 方法发送该图片消息。
            WebIM.conn
              .send(msg)
              .then((res) => {
                // 图片消息成功发送。
                console.log("Success");
              })
              .catch((e) => {
                // 图片消息发送失败。
                console.log("Fail");
              });
          },
        });
      }
    },
  });
}
```

#### 发送 URL 图片消息

你也可以将图片上传到自己的服务器，而不是环信服务器，然后调用 `sendPrivateUrlImg` 方法传入图片的 URL 发送图片消息。

发送 URL 图片消息前，确保将 `Connection` 类中的 `useOwnUploadFun` 设置为 `true`。

```javascript
function sendPrivateUrlImg() {
  let option = {
    chatType: "singleChat",
    // 消息类型。
    type: "img",
    // 图片文件的 URL 地址。
    url: "img url",
    // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
    to: "username",
  };
  // 创建一条图片消息。
  let msg = WebIM.message.create(option);
  // 调用 `send` 方法发送该图片消息。
  WebIM.conn.send(msg);
}
```

#### 发送视频消息

在发送视频消息之前，应在 app 级别实现视频捕获以及捕获文件的上传。

参考以下代码示例创建和发送视频消息：

```javascript
function sendPrivateVideo(){
			var me = this;
			var token = WebIM.conn.context.accessToken
			wx.chooseVideo({
				sourceType: ["album", "camera"],
				maxDuration: 30,
				camera: "back",
				success(res){
					var tempFilePaths = res.tempFilePath;
					var str = WebIM.config.appkey.split("#");
					var domain = wx.WebIM.conn.apiUrl + '/'
					wx.uploadFile({
						url: domain + str[0] + "/" + str[1] + "/chatfiles",
						filePath: tempFilePaths,
						name: "file",
						header: {
							Authorization: "Bearer " + token
						},
						success(res){
							var data = res.data;
							var dataObj = JSON.parse(data);
  						var option = {
                  // 消息类型。
									type: "video",
                  // 会话类型：单聊、群聊和聊天室分别为 `singleChat`、`groupChat` 和 `chatRoom`。
									chatType: "singleChat",
                  // 文件名。
                  filename: "filename",
                  // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
									to: "username",
                  body: {
                    //文件 URL。
                    url:dataObj.uri + "/" + dataObj.entities[0].uuid,
                    //文件类型。
                    type: "video",
                    //文件名称。
                    filename: "filename",
                  },
							}
              let msg = WebIM.message.create(option);
               // 调用 `send` 方法发送该视频消息。
              WebIM.conn.send(msg).then((res)=>{
               // 视频消息成功发送。
                console.log("Success");
              }).catch((e)=>{
                // 视频消息发送失败。
                console.log("Fail");
              });
						}
					});
				}
			});
		},
```

#### 发送文件消息

发送文件消息前，应先选择文件。微信小程序仅支持从客户端会话选择文件。

参考以下代码示例创建、发送和接收文件消息：

```javascript
// 发送文件消息。
function sendFileMessage() {
      const me = this;
      // 微信小程序仅支持从客户端会话选择文件。
      wx.chooseMessageFile({
        // 可选择的最大文件数。
        count: 1,
        success(res) {
          const domain = wx.WebIM.conn.apiUrl + "/";
          const [orgName, appName] = WebIM.config.appkey.split("#");
          const token = WebIM.conn.context.accessToken;
          const tempFiles = res.tempFiles[0];
          const fileName = tempFiles.name;
          const fileSize = tempFiles.size;
          const filePath = tempFiles.path;
          const index = fileName.lastIndexOf(".");
          const filetype = (~index && fileName.slice(index + 1)) || "";

          // 上传文件到服务器。
          wx.uploadFile({
            url: domain + orgName + "/" + appName + "/chatfiles",
            filePath: filePath,
            name: "file",
            header: {
              Authorization: "Bearer " + token
            },
            success(res) {
              // 获取返回数据。
              let data = res.data;
              let dataObj = JSON.parse(data);
              let option = {
                // 消息类型。
                type: "file",
                // 单聊、群聊和聊天室分别为 `singleChat`、`groupChat` 和 `chatRoom`。
                chatType: "singleChat",
                // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
                to: "userId",
                body: {
                  // 文件 URL。
                  url: dataObj.uri + "/" + dataObj.entities[0].uuid,
                  // 文件类型。
                  type: filetype,
                  // 文件名称。
                  filename: fileName,
                  // 文件大小。
                  file_length: fileSize
                }
              };
              
              // 创建消息。
              const msg = WebIM.message.create(option);
              // 发送消息。
              WebIM.conn
                .send(msg)
                .then((res) => {
                  console.log(res, "send file message success");
                })
                .catch((e) => {
                  console.log(e, "send file message error");
                });
            }
          });
        }
      });
    }
```

### 发送位置消息

当你需要发送位置时，需要集成第三方的地图服务，获取到位置点的经纬度信息。接收方接收到位置消息时，需要将该位置的经纬度，借由第三方的地图服务，将位置在地图上显示出来。

```javascript
const sendLocMsg = () => {
  let option = {
    chatType: "singleChat",
    type: "loc",
    to: "username",
    addr: "四通桥东",
    buildingName: "数码大厦",
    lat: 40, // 纬度
    lng: 116, // 经度
  };
  let msg = WebIM.message.create(option);
  conn.send(msg).then((res)=>{
        console.log("Send message success"，res);
    }).catch((e)=>{
        console.log("Send message fail"，e);
    });
};
```

### 发送透传消息

透传消息是通知指定用户采取特定操作的命令消息。接收方自己处理透传消息。

:::tip
透传消息发送后，不支持撤回。
:::

参考以下代码示例发送和接收透传消息：

```javascript
function sendCMDMessage() {
  let option = {
    // 消息类型。
    type: "cmd",
    // 会话类型：单聊、群聊和聊天室分别为 `singleChat`、`groupChat` 和 `chatRoom`。
    chatType: "singleChat",
    // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
    to: "username",
    // 自定义动作。对于透传消息，该字段必填。
    action: "action",
    // 消息扩展信息。
    ext: { key: "extends messages" },
  };
  // 创建一条透传消息。
  let msg = WebIM.message.create(option);
  // 调用 `send` 方法发送该透传消息。
  WebIM.conn
    .send(msg)
    .then((res) => {
      // 消息成功发送回调。
      console.log("Success");
    })
    .catch((e) => {
      // 消息发送失败回调。
      console.log("Fail");
    });
}
```

#### 通过透传消息实现输入指示器

输入指示器显示其他用户何时输入消息。通过该功能，用户之间可进行有效沟通，设定对聊天应用程序中新交互的期望。你可以通过透传消息实现输入指示器。

你可以通过透传消息实现输入指示器。下图为输入指示器的工作原理。

![img](/images/common/typing_indicator.png)

监听用户 A 的输入状态。一旦有文本输入，通过透传消息将输入状态发送给用户 B，用户 B 收到该消息，了解到用户 A 正在输入文本。

- 用户 A 向用户 B 发送消息，通知其开始输入文本。
- 收到消息后，如果用户 B 与用户 A 的聊天页面处于打开状态，则显示用户 A 的输入指示器。
- 如果用户 B 在几秒后未收到用户 A 的输入，则自动取消输入指示器。

:::notice
用户 A 可根据需要设置透传消息发送间隔。
:::

以下示例代码展示如何发送输入状态的透传消息。

发送输入状态的用户。

```typescript
let previousChangedTimeStamp = 0;
// 监听输入状态的变化
const onInputChange = function () {
  const currentTimestamp = new Date().getTime();
  if (currentTimestamp - previousChangedTimeStamp > 5000) {
    sendBeginTyping();
    previousChangedTimeStamp = currentTimestamp;
  }
};

// 创建输入状态消息并发送
const sendBeginTyping = function () {
  const option = {
    // 会话类型：单聊、群聊和聊天室分别为 `singleChat`、`groupChat` 和 `chatRoom`。
    chatType: "singleChat", 
    // 消息类型。
    type: "cmd", 
    // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
    to: "<target id>", 
    // 用户自定义操作。
    action: "TypingBegin", 
  };
  const typingMessage = message.create(option);

  connection
    .send(typingMessage)
    .then(() => {
      console.log("success");
    })
    .catch((e) => {
      console.log("fail");
    });
};
```

接收输入状态的用户。

```typescript
// 设置状态监听器
let timer;
conn.addEventHandler("message", {
  onCmdMessage: (msg) => {
    console.log("onCmdMessage", msg);
    if (msg.action === "TypingBegin") {
      // 这里需更新 UI，显示“对方正在输入”
      beginTimer();
    }
  },
});

const beginTimer = () => {
  timer && clearTimeout(timer);
  timer = setTimeout(() => {
    // 这里需更新 UI，不再显示“对方正在输入”
  }, 5000);
};
```

### 发送自定义消息

自定义消息为用户自定义的键值对，包括消息类型和消息内容。

参考以下示例代码创建和发送自定义消息：

```javascript
function sendCustomMsg() {
  // 设置自定义事件。
  let customEvent = "customEvent";
  // 通过键值对设置自定义消息内容。
  let customExts = {};
  let option = {
    // 消息类型。
    type: "custom",
    // 消息接收方：单聊为对方用户 ID，群聊和聊天室分别为群组 ID 和聊天室 ID。
    to: "username",
    // 会话类型：单聊、群聊和聊天室分别为 `singleChat`、`groupChat` 和 `chatRoom`。
    chatType: "singleChat",
    customEvent,
    customExts,
    // 消息扩展字段，不能设置为空，即设置为 "ext:null" 会出错。
    ext: {},
  };
  // 创建一条自定义消息。
  let msg = WebIM.message.create(option);
  // 调用 `send` 方法发送该自定义消息。
  WebIM.conn
    .send(msg)
    .then((res) => {
      // 消息成功发送回调。
      console.log("Success");
    })
    .catch((e) => {
      // 消息发送失败回调。
      console.log("Fail");
    });
}
```

### 发送合并消息

为了方便消息互动，即时通讯 IM 自 4.2.0 版本开始支持将多个消息合并在一起进行转发。你可以采取以下步骤进行消息的合并转发：

1. 利用原始消息列表创建一条合并消息。
2. 发送合并消息。
3. 对端收到合并消息后进行解析，获取原始消息列表。

:::notice

该功能在 uniapp 中暂不支持运行到原生手机端。

:::
#### 创建和发送合并消息

你可以调用 `message.create` 方法创建一条合并消息，然后调用 `connection.send` 方法发送该条消息。

创建合并消息时，需要设置以下参数：

| 属性                   | 类型                                            | 描述         |
| :--------------------- | :---------------------------------------------- | :----------------------- |
| `chatType`             | ChatType                                        | 会话类型。     |
| `type`                 | 'combine'                                       | 消息类型。    |
| `to`                   | String                                          | 消息接收方。该字段的设置取决于会话类型：<br/> - 单聊：对方用户 ID；<br/> - 群聊：群组 ID；<br/> - 子区会话：子区 ID；<br/> - 聊天室聊天：聊天室 ID。    |
| `title`                | String                                          | 合并消息的标题。   |
| `summary`              | String                                          | 合并消息的概要。   |
| `compatibleText`       | String                                          | 合并消息的兼容文本。<br/>兼容文本起向下兼容不支持消息合并转发的版本的作用。当支持合并消息的 SDK 向不支持合并消息的低版本 SDK 发送消息时，低版本的 SDK 会将该属性解析为文本消息的消息内容。 |
| `messageIdList`        | MessagesType[]                                  | 合并消息的原始消息 ID 列表。该列表最多包含 300 个消息 ID。        |
| `onFileUploadComplete` | (data: { url: string; secret: string;}) => void | 合并消息文件上传完成的回调。     |
| `onFileUploadError`    | (error: any) => void                            | 合并消息文件上传失败的回调。      |

:::notice
1. 合并转发支持嵌套，最多支持 10 层嵌套，每层最多 300 条消息。
2. 只有成功发送或接收的消息才能合并转发。
:::

示例代码如下：

```javascript
let option = {
  chatType: "singleChat",
  type: "combine",
  to: "userId",
  compatibleText: "SDK 版本低，请升级",
  title: "聊天记录",
  summary: "hi",
  messageList: [
    {
      type: "txt",
      // ...
    },
  ],
  onFileUploadComplete: (data) => {
    option.url = data.url;
  },
};
let msg = WebIM.message;
conn.send
  .send(msg)
  .then((res) => {
    console.log("发送成功", res);
  })
  .catch((err) => {
    console.log("发送失败", err);
  });
```

接收合并消息与[接收普通消息](#接收消息)的操作相同，唯一不同是对于合并消息来说，消息接收事件为 `onCombineMessage`。

对于不支持合并转发消息的 SDK 版本，该类消息会被解析为文本消息，消息内容为 `compatibleText` 携带的内容，其他字段会被忽略。

#### 解析合并消息

合并消息实际上是一种附件消息。收到合并消息后，你可以调用 `downloadAndParseCombineMessage` 方法下载合并消息附件并解析出原始消息列表。

```javascript
connection
  .downloadAndParseCombineMessage({
    url: msg.url,
    secret: msg.secret,
  })
  .then((res) => {
    console.log("合并消息解析后的消息列表", res);
  });
```

### 发送定向消息

发送定向消息是指向群组或聊天室的单个或多个指定的成员发送消息，其他成员不会收到该消息。

该功能适用于文本消息、图片消息和音视频消息等全类型消息，最多可向群组或聊天室的 20 个成员发送定向消息。

:::notice
1. 仅 SDK 4.2.0 及以上版本支持。
2. 定向消息不写入服务端会话列表，不计入服务端会话的未读消息数。
3. 定向消息不支持消息漫游功能，因此从服务器拉取漫游消息时，不包含定向消息。
:::

发送定向消息的流程与发送普通消息相似，唯一区别是需要设置定向消息的接收方。

下面以文本消息为例介绍如何发送定向消息，示例代码如下：

```javascript
// 发送定向文本消息。
function sendTextMessage() {
  let option = {
    // 消息类型。
    type: "txt",
    // 消息内容。
    msg: "message content",
    // 消息接收方所在群组或聊天室的 ID。
    to: "groupId",
    // 会话类型：群聊和聊天室分别为 `groupChat` 和 `chatRoom`。
    chatType: "groupChat",
    // 消息的接收方列表。最多可传 20 个接收方的用户 ID。若不设置该字段或传入数组类型之外的值，如字符串，则消息发送给群组或聊天室的所有成员。
    receiverList: ['uId1','uId2'],
  };
  // 创建文本消息。
  let msg = WebIM.message.create(option);
  // 调用 `send` 方法发送该文本消息。
    conn.send(msg).then((res)=>{
      console.log("Send message success",res);
    }).catch((e)=>{
      console.log("Send message fail",e);
    });
}
```

接收定向消息与接收普通消息的操作相同，详见[接收消息](#接收消息)。

### 使用消息扩展

如果上述类型的消息无法满足要求，你可以使用消息扩展为消息添加属性。这种情况可用于更复杂的消息传递场景，例如消息中需要携带被回复的消息内容或者是图文消息等场景。

```javascript
function sendTextMessage() {
  let option = {
    type: "txt",
    msg: "message content",
    to: "username",
    chatType: "singleChat",
    // 设置消息扩展信息。扩展字段为可选，若带有该字段，值不能为空，即 "ext:null" 会出错。
    ext: {
      key1: "Self-defined value1",
      key2: {
        key3: "Self-defined value3",
      },
    },
  };
  let msg = WebIM.message.create(option);
  // 调用 `send` 方法发送该扩展消息。
  WebIM.conn
    .send(msg)
    .then((res) => {
      console.log("send private text Success");
    })
    .catch((e) => {
      console.log("Send private text error");
    });
}
```
