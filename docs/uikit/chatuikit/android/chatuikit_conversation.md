# 会话列表页面

## 页面组件

会话列表页面由 `ChatUIKitConversationListFragment` 实现，包括标题栏、搜索区域和会话列表。

<ImageGallery>
  <ImageItem src="/images/uikit/chatuikit/android/custom_conversation_list.png" title="会话列表" />
</ImageGallery>

### 标题栏

会话列表页面与聊天页面、联系人列表页面、群详情页面、联系人详情页面的标题栏均使用 `ChatUIKitTitleBar`。标题栏包含左、中、右三个区域，标题栏中的标题、头像、背景色、标题栏右侧按钮的显示图片和左侧的头像均可自定义，详见 [设置标题栏](#设置标题栏)。

### 会话搜索栏

会话搜索栏 `ChatUIKitSearchView` 实现会话搜索。点击搜索按钮，跳转到搜索页面，可按会话名称搜索会话。

### 会话列表

会话列表组件 `ChatUlKitConversationListLayout` 实现按会话中最新一条消息的时间（或改为时间）的倒序显示所有会话，包括：
- 通过标题栏右侧的加号创建的本地会话。
- 两个用户之间发送消息后创建的单聊会话。
- 群组中发送消息后创建的群组会话。

在会话列表中，置顶的会话排在列表最上方。

### 会话列表项

会话列表项组件 `UikitItemConversationListBinding` 实现单条会话展示，包括会话名称、最后一条消息、最后一条消息的时间以及置顶和禁言状态等。

- 对于单聊, 会话展示的名称为对端用户的昵称，若对端用户未设置昵称则展示对方的用户 ID；会话头像是对方的头像，如果没有设置则使用默认头像。
- 对于群聊，会话名称为当前群组的名称，头像为默认头像。

1. 点击会话列表项，跳转到会话详情页面。

2. 长按会话列表中的会话列表项会显示会话操作弹窗，`ChatUIKitConversationListFragment` 中默认实现以下会话操作：

<ImageGallery>
  <ImageItem src="/images/uikit/chatuikit/android/conversation_long_press.png" title="会话长按显示的操作" />
</ImageGallery>

- **会话免打扰**：使用 `ChatUIKitConversationListViewModel` 提供的方法设置免打扰，例如:

  - `makeSilentForConversation`：设置会话免打扰。
  - `cancelSilentForConversation` ：取消会话免打扰。

- **会话置顶**：使用 `ChatUIKitConversationListViewModel` 提供的方法设置会话置顶，例如:

  - `pinConversation`：置顶一个会话。
  - `unpinConversation`：取消会话置顶。

- **会话标记已读**：使用 `ChatUIKitConversationListViewModel` 提供的 `makeConversionRead` 方法标记会话已读。

- **会话删除**：使用 `ChatUIKitConversationListViewModel` 提供的方法 `deleteConversation` 方法删除会话。

建议你在首次下载、卸载后重装应用等本地数据库无数据情况下拉取服务端会话列表。

## 创建会话列表页面

单群聊 UIKit 提供 `ChatUIKitConversationListFragment`，添加到 Activity 中即可使用。

示例如下：

```kotlin
class ConversationListActivity: AppCompactActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_conversation_list)

        ChatUIKitConversationListFragment.Builder()
                        .build()?.let { fragment ->
                            supportFragmentManager.beginTransaction()
                                .replace(R.id.fl_fragment, fragment).commit()
                        }
    }
}
```

## 自定义设置概览

`ChatUIKitConversationListFragment` 提供了 Builder 构建方式，方便开发者进行一些自定义设置，目前提供的设置项如下：

```kotlin
ChatUIKitConversationListFragment.Builder()
    .useTitleBar(true)
    .setTitleBarTitle("title")
    .enableTitleBarPressBack(true)
    .setTitleBarBackPressListener(onBackPressListener)
    .useSearchBar(false)
    .setItemClickListener(onItemClickListener)
    .setOnItemLongClickListener(onItemLongClickListener)
    .setOnMenuItemClickListener(onMenuItemClickListener)
    .setConversationChangeListener(conversationChangeListener)
    .setEmptyLayout(R.layout.layout_conversation_empty)
    .setCustomAdapter(customAdapter)
    .setCustomFragment(myConversationListFragment)
    .build()
```

`ChatUIKitConversationListFragment#Builder` 提供的方法如下表所示：

| 方法                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| useTitleBar()                      | 是否使用默认的标题栏（`ChatUIKitTitleBar`）。<br/> - `true`：是。 <br/> - (默认) `false`: 否。           |
| setTitleBarTitle()                 | 设置标题栏的标题。                                            |
| enableTitleBarPressBack()          | 设置是否支持显示返回按钮，默认为不显示返回按钮。<br/> - `true`：是。 <br/> - (默认) `false`: 否。              |
| setTitleBarBackPressListener()    | 设置点击标题栏返回按钮的监听器。                               |
| setItemClickListener()          | 设置条目点击事件监听器。                                       |
| setOnItemLongClickListener()    | 设置条目长按事件监听器。                                       |
| setOnMenuItemClickListener()    | 设置条目菜单点击事件监听器。                                       |
| setConversationChangeListener() | 设置会话变化的监听器。                                        |
| setEmptyLayout()                | 设置会话列表的空白页面。                                       |
| setCustomAdapter()              | 设置自定义的适配器，默认为 `ChatUIKitConversationListAdapter`。       |
| setCustomFragment()             | 设置自定义聊天 `Fragment`，需要继承自 `ChatUIKitConversationListFragment`。 |

## 添加自定义会话布局 // TODO：自定义会话列表

开发者可以继承 `ChatUIKitConversationListAdapter` 实现自己的 `CustomConversationListAdapter`，然后将 `CustomConversationListAdapter` 设置到 `ChatUIKitConversationListFragment#Builder#setCustomAdapter` 中。

1. 创建自定义适配器 `CustomConversationListAdapter`，继承自 `ChatUIKitConversationListAdapter`，重写 `getViewHolder` 和 `getItemNotEmptyViewType` 方法。

```kotlin
class CustomConversationListAdapter (
    config: ChatUIKitConvItemConfig = ChatUIKitConvItemConfig()
): ChatUIKitConversationListAdapter(config) {
    companion object {
        private const val typeSingleChat = 1
        private const val typeGroupChat = 2
    }

    override fun getItemNotEmptyViewType(position: Int): Int {
        //伪代码
        //假设这里以不同的conversationType返回不同的viewType示例，你可以根据你自己的业务需求调整
        val conversationType = getItem(position)?.conversationType
        when (conversationType) {
            ChatConversationType.Chat -> {
                return typeSingleChat
            }
            ChatConversationType.GroupChat -> {
                return typeGroupChat
            }
            else -> {
                return typeSingleChat
            }
        }
    }

    override fun getViewHolder(
        parent: ViewGroup,
        viewType: Int
    ): ViewHolder<ChatUIKitConversation> {
        //伪代码
        when (viewType) {
            typeSingleChat -> {
                return MySingleChatViewHolder(
                    MySingleChatItemViewBinding
                        .inflate(LayoutInflater.from(parent.context), parent, false), config
                )
            }
            else -> {
                return MyGroupChatViewHolder(
                    MyGroupChatItemViewBinding
                        .inflate(LayoutInflater.from(parent.context), parent, false), config
                )
            }
        }
    }
}
```
```kotlin
class MySingleChatViewHolder( private val viewBinding: MySingleChatItemViewBinding,
                              var config: ChatUIKitConvItemConfig? = ChatUIKitConvItemConfig()
) : ChatUIKitBaseRecyclerViewAdapter.ViewHolder<ChatUIKitConversation>(binding = viewBinding) {

    init {
        // 这里可以进行一些初始化操作
        // 比如设置特定的样式或配置
        config?.bindView(viewBinding)
    }

    override fun initView(itemView: View?) {
        super.initView(itemView)
        // 这里可以进行一些视图的初始化操作
    }

    override fun setData(item: ChatUIKitConversation?, position: Int) {
        //根据你的UI来设置数据
    }
}
```

2. 添加 `CustomConversationListAdapter` 到 `ChatUIKitConversationListFragment#Builder`。

```kotlin
var mConversationListFragment = ChatUIKitConversationListFragment.Builder()
    .setCustomAdapter(CustomConversationListAdapter())
    .build()
```

3. 通过继承 `ChatUIKitConversationListFragment` 进行自定义设置。

创建自定义 `CustomConversationListFragment`，继承自 `ChatUIKitConversationListFragment`，并设置到 `ChatUIKitConversationListFragment#Builder` 中。

```kotlin
builder.setCustomFragment(customConversationListFragment);
```

<ImageGallery :columns="3">
  <ImageItem src="/images/uikit/chatuikit/android/conversation_list_custom_all.png" title="会话列表完整展示" />
  <ImageItem src="/images/uikit/chatuikit/android/conversation_list_custom_noavatar.png" title="会话列表无头像" />
  <ImageItem src="/images/uikit/chatuikit/android/conversation_list_custom_noavatarsutbtitle.png" title="会话列表无头像、无最新消息" />
</ImageGallery>

## 设置标题栏

会话列表页面、聊天页面、联系人列表页面、群详情页面和联系人详情页面的标题栏均使用 `ChatUIKitTitleBar`。如果会话列表页面的标题栏不满足需求，建议根据自身需求设置标题栏。

会话列表页面的标题栏包含左、中、右三个区域，本节介绍如何在使用 `ChatUIKitConversationListFragment` 的前提下配置这些区域。

### 设置是否启用标题栏

```kotlin

//是否使用默认的标题栏（ChatUIKitTitleBar）：true：是；(默认) false: 否。
ChatUIKitConversationListFragment.Builder().useTitleBar()
    
```

### 设置标题栏背景色

设置标题栏的背景色：

```kotlin

binding?.titleConversations?.setBackgroundColor(ContextCompat.getColor(mContext,R.color.blue))
    
```

### 设置左侧头像

```kotlin
//使用 binding?.titleConversations 可以直接获取到 ChatUIKitTitleBar

binding?.titleConversations?.let { titlebar->
    // 获取 logoView
    titlebar.getLogoView()
    // 设置头像
    titlebar.setLogo()
    // 获取 StatusView 
    titlebar.getStatusView()
    // 设置用户状态
    titlebar.setLogoStatus()
    // 设置用户状态的外间距
    titlebar.setLogoStatusMargin()
    // 设置用户状态图标大小
    titlebar.setLogoStatusSize()
}
```

### 设置左侧头像及文本区域点击事件

// TODO：这里的“文本区域点击事件” 是头像点击事件？

```kotlin
// logo 图标区域点击事件 
binding?.titleConversations?.setLogoClickListener {}
// logo status 文本区域点击事件
binding?.titleConversations?.setTitleClickListener {}
    
```

### 设置中部标题

// TODO：下面是设置文本或将文本替换为图片吧
// TODO：添加如何设置中部标题字体和标题文本颜色

```kotlin
// 文本设置
ChatUIKitConversationListFragment.Builder().setTitleBarTitle("title")
// 图片设置
binding?.titleConversations?.setTitleEndDrawable(R.drawable.conversation_title)
    
```

### 设置右侧图标及显示的操作

// TODO：如何替换右侧图标。
// TODO：如何设置多个图标。
// TODO：下面只写了如何添加、隐藏 menu、


// TODO：点击右侧图标后，会弹出菜单，需要明确这个菜单中支持的自定义的内容，包括
// TODO：发起新会话图标、添加联系人图标、创建群组图标、加入公开群组图标、文字颜色、字体、背景颜色、点击动作


一般情况下，右侧会支持设置多个图标。我们采用设置 Menu 的方式进行设置。

`ChatUIKitConversationListFragment` 中有默认实现一个 `defaultMenu()` 的方法添加默认的 menu 菜单。若默认菜单不满足需求，可以替换为自己的 menu 菜单，重写 `defaultMenu()` 方法。   

```kotlin
    // 添加 menu
    override fun defaultMenu() {
        // 自定义满足自身需求的 menu 文件
        binding?.titleConversations?.inflateMenu(R.menu.my_menu)
    }

    // 设置 menu 点击事件
    override fun setMenuItemClick(item: MenuItem): Boolean {
        when(item.itemId) {
            R.id.action_my_menu -> {
                // todo：实现点击指定 menu 后的逻辑处理
                return true
            }
            else -> return false
        }
    }

    // 支持通过 tint 属性设置 menu icon 颜色
    setMenuIconTint(@ColorInt colorInt: Int)
    // 支持设置 menu 文本颜色
    setMenuTitleColor(@ColorInt colorInt: Int)
    // 支持设置隐藏/显示指定 menu 项
    setMenuIconVisible(id:Int,visible:Boolean)

```

### 设置返回按钮和事件监听

// TODO：是标题栏左侧的按钮？是否支持替换返回按钮？仅支持显示或隐藏？

```kotlin

//设置是否支持显示返回按钮：true：是；(默认) false: 否。   
ChatUIKitConversationListFragment.Builder().enableTitleBarPressBack()
//设置点击标题栏返回按钮的监听器。 
ChatUIKitConversationListFragment.Builder().setTitleBarBackPressListener() 
    
```

## 设置搜索区域

### 设置是否需要搜索功能

```kotlin

// 是否使用默认的搜索功能（跳转 ChatUIKitSearchActivity 搜索页面）。目前支持搜索用户、会话、消息、黑名单用户。
// true：是；(默认) false: 否。 
ChatUIKitConversationListFragment.Builder().useSearchBar(true)   
```

### 自定义搜索

// TODO：搜索部分需要提供以下自定义：
1. 搜索图标
2. 搜索框占位符字体
3. 搜索框文本颜色
4. 搜索框背景颜色

如果默认的搜索无法满足用户需求，可以通过 `setCustomActivityRoute` 修改跳转路由，跳转自己的搜索页面。

```kotlin
ChatUIKitClient.setCustomActivityRoute(object : ChatUIKitCustomActivityRoute {
    override fun getActivityRoute(intent: Intent): Intent? {
        intent.component?.className?.let {
             when(it) {
                ChatUIKitSearchActivity::class.java.name -> {   
                    intent.setClass(context, MySearchActivity::class.java)    
                }
                else -> {
                    return intent
                }
             }
        }
    }
})

    
```

## 设置会话列表项

要设置会话列表中列表项的内容，你需要执行以下步骤：

需要先获取到 `ChatUIKitConversationListLayout` 对象，该对象提供了更加细致的设置项：

```kotlin
    binding?.listConversation?.let{
        it.setItemBackGround()      //设置条目的背景。
        it.setItemHeight()          //设置条目的高度。
        it.setAvatarDefaultSrc()    //设置条目的默认头像。
        it.setAvatarSize()          //设置条目头像的大小。
        it.setAvatarShapeType()     //设置条目头像的样式，分为默认 ImageView 样式，圆形和矩形三种样式。
        it.setAvatarRadius()        //设置条目头像的圆角半径，样式设置为矩形时有效。
        it.setAvatarBorderWidth()   //设置条目头像边框的宽度。 
        it.setAvatarBorderColor()   //设置条目头像边框的颜色。
        it.setNameTextSize()        //设置会话条目标题的文字大小。
        it.setNameTextColor()       //设置会话条目标题的文字颜色。 
        it.setMessageTextSize()     //设置会话条目内容的文字大小。
        it.setMessageTextColor()    //设置会话条目内容的文字颜色。 
        it.setDateTextSize()        //设置会话条目日期的文字大小。
        it.setDateTextColor()       //设置会话条目日期的文字颜色。

        it.setListAdapter()         //设置自定义会话列表适配器。
        it.getListAdapter()         //获取会话列表适配器。 
        it.addHeaderAdapter()       //添加会话列表的头布局的适配器。
        it.addFooterAdapter()       //添加会话列表的尾布局的适配器。
        it.addItemDecoration()      //添加会话列表的装饰器。
        it.removeItemDecoration()   //移除会话列表的装饰器。
        it.addItemMenu()            //添加长按单项。 
        it.clearMenu()              //清除长按菜单项。
        it.findItemVisible()        //设置指定菜单项是否可见。   
    }
```



### 设置会话列表项背景

```kotlin
    binding?.listConversation?.let{
        it.setItemBackGround()      //设置条目的背景。   
    }
```

### 设置会话列表项高度

```kotlin
    binding?.listConversation?.let{
        it.setItemHeight()          //设置条目的高度。
    }
```

### 设置会话列表项头像

// TODO：可以设置显示隐藏头像吗？

你可以设置会话列表项的默认头像，以及头像的大小、样式、圆角半径、头像边框宽度和颜色。

// TODO：这句话还要吗？关于设置会话头像和昵称，详见[用户自定义信息文档中的介绍](chatuikit_userinfo.html#设置会话头像和昵称)。

```kotlin
    binding?.listConversation?.let{
        it.setAvatarDefaultSrc()    //设置条目的默认头像。
        it.setAvatarSize()          //设置条目头像的大小。
        it.setAvatarShapeType()     //设置条目头像的样式，分为默认 ImageView 样式，圆形和矩形三种样式。
        it.setAvatarRadius()        //设置条目头像的圆角半径，样式设置为矩形时有效。
        it.setAvatarBorderWidth()   //设置条目头像边框的宽度。 
        it.setAvatarBorderColor()   //设置条目头像边框的颜色。
    }
```

### 设置会话列表项标题

// TODO：这么说对吗？

默认情况下，会话列表项的标题取决于会话类型：
- 单聊会话：对端用户的昵称。
- 群聊会话：群组名称。

// TODO：可以设置标题的字体吗？

```kotlin
    binding?.listConversation?.let{
        it.setNameTextSize()        //设置会话条目标题的文字大小。
        it.setNameTextColor()       //设置会话条目标题的文字颜色。 
        it.setMessageTextSize()     //设置会话条目内容的文字大小。
        it.setMessageTextColor()    //设置会话条目内容的文字颜色。  
    }
```

### 设置会话列表项内容

默认情况下，会话列表项的内容为单聊和群聊会话中的最新一条消息。

// TODO：可以设置标题和副标题的字体吗？

```kotlin
    binding?.listConversation?.let{
        it.setMessageTextSize()     //设置会话条目内容的文字大小。
        it.setMessageTextColor()    //设置会话条目内容的文字颜色。  
    }
```

### 设置会话列表项日期

```kotlin
    binding?.listConversation?.let{ 
        it.setDateTextSize()        //设置会话条目日期的文字大小。
        it.setDateTextColor()       //设置会话条目日期的文字颜色。
    }
```

### 设置会话列表项长按菜单

长按会话列表项，弹出的菜单默认包括会话免打扰、会话置顶、会话标记已读和会话删除。

你可以添加菜单项、清除所有菜单项以及设置指定菜单项是否可见。

// TODO：需要添加如何设置菜单的文字颜色、字体和提示框的背景颜色。

```kotlin
    binding?.listConversation?.let{
        it.addItemMenu()            //添加长按单项。 
        it.clearMenu()              //清除长按菜单项。
        it.findItemVisible()        //设置指定菜单项是否可见。   
    }
```

### 设置消息未读计数图标

// TODO：需要补充，是否可以设置，有没有默认的图标，是否可以隐藏。

### 设置会话免打扰图标

// TODO：需要补充，是否可以设置，有没有默认的图标，是否可以隐藏。

### 设置会话列表项其他配置

适配器、装饰器

### 设置会话页面的 footer // TODO：有个名称吗？

// TODO：这部分包括：聊天按钮图标、联系人按钮图标、个人资料按钮图标、背景颜色、聊天按钮动作、联系人按钮动作、个人资料按钮动作

## 事件监听

`ChatUIKitConversationListFragment#Builder` 提供的如下监听：

```kotlin
ChatUIKitConversationListFragment.Builder()
    .setTitleBarBackPressListener()
    .setItemClickListener(onItemClickListener)
    .setOnItemLongClickListener(onItemLongClickListener)
    .setOnMenuItemClickListener(onMenuItemClickListener)
    .setConversationChangeListener(conversationChangeListener)
    .build()
```

| 方法                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------|
| setTitleBarBackPressListener()  | 设置点击标题栏返回按钮的监听器。                                 |
| setItemClickListener()          | 设置条目点击事件监听器。                                        |
| setOnItemLongClickListener()    | 设置条目长按事件监听器。                                        |
| setOnMenuItemClickListener()    | 设置条目菜单点击事件监听器。                                    |
| setConversationChangeListener() | 设置会话变化的监听器。                                         |










