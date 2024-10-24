# 环信 AIGC 方案选择

本文提供两种方案供你接入环信 AI 聊天服务，实现与机器人的聊天功能：

- **方案一**：利用服务器回调服务，在 IM 中引入 AI 服务。
- **方案二**：使用环信即时通讯云提供的 AI 智能功能。

## 方案一

通过配置服务端和客户端，利用环信即时通信 IM 服务器回调功能，在 IM 中引入 AI 服务（以 MiniMax 中文大语言模型为例），创建机器人账号，从而跑通示例项目。

### 技术架构

目前，环信 AIGC 方案可通过服务器回调服务实现与机器人聊天，以单聊为例，工作流程如下：

1. 用户发消息给机器人。
2. 环信服务器收到消息后，通过服务端回调将事件通知第三方大模型厂商。
3. 第三方大模型厂商收到事件通知，将消息回复内容发送给 app server。
4. app server 调用 Restful API 将回复内容发送给环信服务器。
5. 环信服务器将回复内容发送给用户。

![img](/images/aigc/technical_architecture1.png)

### 跑通示例项目

完成客户端和服务端配置，跑通 [GitHub 示例项目](https://github.com/easemob/Easemob-AIGCService-Example)，体验与 AIGC 数字人沟通方案。

有关更多信息，请参见[跑通示例项目文档说明](aigc_run_through_demo_server.html)。

## 方案二

使用环信即时通讯 IM 提供的 AI 机器人聊天。你需要在[环信即时通讯控制台](https://console.easemob.com/user/login)开通 AI 智能功能，创建机器人，即实现与机器人的**单聊**功能。

此外，你可以在[环信即时通讯控制台](https://console.easemob.com/user/login)查看机器人账号消耗的 token 数和消耗趋势。

有关更多信息，请参见[使用 AI 机器人聊天](aigc_use.html)和[调用 REST API 获取 app 下的机器人列表](aigc_rest_api.html)相关文档。

### 技术架构

目前，环信 AI 智能功能**仅支持单聊**，工作流程如下：

1. 用户发消息给机器人。
2. 环信服务器收到消息后，通过环信机器人服务将用户消息传递给第三方大模型厂商。
3. 第三方大模型厂商对用户消息进行回复，将消息回复内容发送给环信机器人服务。
4. 环信机器人服务收到消息后，通过环信服务器将回复内容发送给用户。

![img](/images/aigc/technical_architecture2.png)


