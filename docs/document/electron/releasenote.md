# 桌面端SDK 更新日志

## 版本 V3.8.4 2021-12-09

仅 V3.8.4 及以下版本支持私有化部署。

新增

- 增加支持自定义消息的收发

## 版本 V3.8.0 2021-03-13

修复

- 去掉音视频功能
- 修复部分Demo bug

## 版本 V3.6.0 2019-07-16

新增

- 1v1音视频会话功能

修复

- 修复了上传附件token过期时，重新获取的token在重试过程中未应用的bug
- 修复了音视频消息体中remotePath路径为空的问题
- 修复Demo UI层的bug

## 版本 V3.5.5 2019-05-09

新增

- sdk 新增私有化部署的接口

修复

- 修复 Demo 在 mac 系统某些版本(如10.12)上，存在兼容性问题，无法启动的问题
- 修改 sdk 收到消息后 ack 确认的发送为消息存储到数据库之后，防止突然崩溃导致的消息丢失
- 修复 Demo UI 层的部分 bug

优化

- 离线消息的下载存储由单条存储改为多条批量存储，提升效率

## 版本：V3.5.4 2019-03-26

实现功能：

- 账户的登录、登出、注册。

- 聊天：单聊、群聊消息收发。

- 消息类型：

1. 可发送：文本、emoji表情、图片、文件。
2. 可接收：文本、emoji表情、图片、文件、位置、语音消息。

- 好友管理：添加好友、删除好友。

- 群管理：创建群、退出群、解散群、修改群标题、邀请加入群、搜索公开群、申请加入公开群。