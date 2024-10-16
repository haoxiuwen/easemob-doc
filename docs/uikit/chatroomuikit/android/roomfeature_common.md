# ChatroomUIKit 通用特性

<Toc />

## 创建聊天室  

ChatroomUIKit 不提供创建聊天室的功能，你可以[调用即时通讯 IM SDK 的 REST API 创建聊天室](/document/server-side/chatroom.html#创建聊天室)。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_create.png" title="聊天室创建" />
</ImageGallery>

## 离开聊天室

ChatroomUIKit 提供离开聊天室功能。聊天室中的成员可自行离开聊天室，聊天室所有者也可以将成员移出聊天室。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_create.png" title="聊天室创建" />
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_destroy.png" title="聊天室销毁" />
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_create.png" title="聊天室创建" />
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_destroy.png" title="聊天室销毁" />
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_create.png" title="聊天室创建" />
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_destroy.png" title="聊天室销毁" />
</ImageGallery>


## 解散聊天室

ChatroomUIKit 不提供解散聊天室的功能，你可以[调用即时通讯 IM SDK 的 REST API 解散聊天室](/document/server-side/chatroom.html#解散聊天室)。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/chatroom_destroy.png" title="解散聊天室" />
</ImageGallery>

## 发送弹幕

用户在聊天室中向其他成员发送文字和表情的消息。发送消息在聊天室中是沟通和交流的基本行为，它使参与者能够分享信息、表达观点、提问、分享内容或与其他人建立联系。

在 UIKit 中，用户通过点击输入框，触发键盘以编辑文本信息，也可以通过点击表情按钮切换键盘，编辑表情信息，然后点击**发送**按钮发送消息。

用户可以根据业务需要选择显示或隐藏消息发送时间（HH:MM 格式）、用户标识和用户头像。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/barrage_send.png" title="发送弹幕" />
</ImageGallery>

## 打赏

用户通过赠送虚拟礼物，向聊天室中的主播或其他用户表达赞赏或者支持的行为。当用户认为某个用户或内容值得赞赏时，可以选择打赏的方式给予对方代表着特定金额的虚拟礼物。

打赏不仅可以作为对其他用户的鼓励和支持，对于主播和直播平台而言，打赏也成为主要收入来源之一。

在 UIKit 中，默认提供了 12 种不同的虚拟礼物。同时，用户可以自定义虚拟礼物的样式、名称和金额。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/gift.png " title="打赏" />
</ImageGallery>

## 全局广播

全局广播即向 App 内所有聊天室中的所有用户发送消息或通知，可用于传达重要信息、公告、提醒或紧急通知等。

全局广播通常会使用明显的标识或格式以区别于普通聊天消息，确保用户能够快速识别全局广播。

你也可以[调用 REST API 发送聊天室全局广播消息](/document/server-side/message_chatroom.html#发送聊天室全局广播消息)。

在 UIKit 中，用户可以自定义全局广播的标识以及发送内容。全局广播消息的默认展示策略如下：

- 若内容未超过一屏宽，无需滚动，停留 3 秒后消失； 
- 若内容超过一屏宽，则需要滚动，先停留 2 秒展示开头部分，然后以每秒 10 个字符的速度进行滚动，滚动完成后再停留 2 秒。
- 若同时有多条全局广播消息，则按消息发送的时间顺序播放，一条消息滚动完成后再播放下一条。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/global_broadcast.png" title="全局广播" />
</ImageGallery>

## 未读消息数

聊天室未读消息数指在一个聊天室中用户尚未读取的消息数量。在 UIKit 中，当用户滑动屏幕回看消息时，消息区域定位将会变更，此时产生的新消息会被标记为未读消息。同时，在消息区域的右下方会出现未读消息按钮。该按钮展示未读消息数，用户点击后直接定位到最新消息位置（通常为消息区域的最底部），确保不错过未读信息。

若未读消息的数量不超过 99，则未读消息数显示实际的数量，若达到 100 及以上则显示 99+。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/message_unread.png" title="未读消息数" />
</ImageGallery>

## 已禁言列表

记录被禁止发言用户的列表。当用户违反了聊天室的规则时，聊天室所有者将其禁言，即添加至已禁言列表。被禁言的成员无法在聊天室中发送消息，但仍留在聊天室，可以查看消息。

在 UIKit 中，默认通过点击聊天室界面右上角的成员按钮，触发已禁言列表。用户在列表中查看聊天室被禁言的所有成员，包括成员的身份、头像和昵称，同时可以通过点击成员右端的管理功能按钮，触发解除禁言选项，实现取消禁言的操作。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/mute_list.png" title="已禁言列表" />
</ImageGallery>


## 暗黑模式

暗黑模式是一种界面设计选择，旨在提供更低对比度和更多暗色调的视觉外观。用户可以根据需要启用或禁用暗黑模式，以实现最佳的使用体验。

UIKit 支持暗黑模式的一键切换。UIKit 默认风格为明亮模式，切换为暗黑模式后，聊天室界面中所有元素将替换为暗黑风格设计，提供用户舒适的视觉体验。

<ImageGallery>
  <ImageItem src="/images/uikit/chatroomfeature/dark_mode.png" title="暗黑模式" />
</ImageGallery>





