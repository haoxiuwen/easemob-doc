﻿# 历史记录

## 前提条件

[开通内容审核服务](moderation_enable.html)。

## 应用场景

**发现某条消息经过审核后的下发结果不符合业务预期，怎么办？**

本文以文本审核服务为例，介绍如何通过查看**历史记录**页面定位问题和解决问题。

**1、进入 历史记录页**

![img](/images/moderation/moderation_history_01.png)

历史记录中的字段详细介绍如下：

| 字段         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| 发送方ID     | 表示内容发送方的环信用户 ID。规则测试页发起的审核，发送方的用户ID默认为 **test**。 |
| 接收方ID     | 表示内容接收方的环信用户 ID                                  |
| 会话类型     | 表示消息内容的会话类型，包括：单聊、群聊、聊天室。           |
| 消息内容     | 表示审核的内容。对于文本消息，显示具体文本内容；对于图片和音视频消息，显示文件链接，点击链接即可查看具体内容。 |
| 消息发送时间 | 表示内容的发送时间。如果是一条消息，则表示消息的发送时间；如果是规则测试页内容，则表示点击立即审核的时间。 |
| 规则名称     | 表示按该条规则配置的策略进行审核处理。                       |
| 消息处置结果 | 表示消息内容的下发处理结果，存在通过/拦截/替换*** 3 种情况。 |
| 审核结果     | 表示消息内容的审核结果；如果成功返回结果，存在通过/拒绝/疑似3种情况；如果指定时间内未返回结果，则记为异常。 |
| 风险类型     | 当审核结果为“拒绝”时，表示发现违规内容，同时会返回违规原因（即风险类型）。 |

**2、查看审核结果**

![img](/images/moderation/moderation_history_02.png)

判断审核结果是否符合业务预期？

- 若不符合，会存在以下两种情况：
  - 实际显示：通过，但预期：拒绝，则可新增自定义词来调整；
  - 实际显示：拒绝，但预期：通过，则需联系商务，由专门运营人员支持调整；
- 若符合，则继续看第3步

**3、查看消息处置结果**

![img](/images/moderation/moderation_history_03.png)

判断消息处置结果是否符合业务预期？

- 若不符合，修改对应的规则配置，详见[规则配置](moderation_rule_config.html)；
- 若符合，说明审核服务的逻辑符合预期，但实际 IM 消息表现效果不一致，则请在IM的支持群中发出问题进行排查解决。