# 环信--小程序直播聊天室
# 介绍
******
环信直播小程序demo是基于环信im SDK开发的一款直播小程序。这个demo可以帮助开发者们更轻松的集成环信SDK。
demo 包含以下功能
  - 主播开播、下播
  - 直播间聊天系统
  - 点赞、礼物系统（示例不包含api）
  - 房间禁言、黑白名单
# 在本地跑起来
拉取代码，导入开发者工具即可运行起来。

# 项目结构
```shell
|- template 自定义组件目录
    |- card 直播间列表（已开播、未开播、立即开播）
    |- gift 礼物组件
    |- memberListModa 成员列表组件（直播间成员、禁言名单、白名单）
|-pages 功能页面
    |-abount 关于直播间
    |-active 开播列表
    |-index 正在直播列表
    |-live 直播间详情页面 （大部分逻辑都在此页面）
|-utils 工具类和sdk的一些配置
|-sdk 环信sdk
|-app.js 小程序根实例，存放一些全局变量，注册监听事件
|-app.json 注册页面以及全局的一些配置
|-app.wxss 一些全局样式
|-project.config.json工程的一些配置，和开发者工具 “详情” 中的设置一样
```
# 可以复用的代码
如果想快速搭建起一个有im能力的直播小程序，可以选择复用demo中的代码，其中utils以帮助快速集成sdk，template组件里面包含礼物系统、直播列表card等

# 常见问题
+ 怎么收发消息 ?（普通消息、自定义消息)
  ### demo中发送普通消息：
   ```
  + 1、let id = wx.WebIM.conn.getUniqueId() // 生成本地消息id
  + 2、let msg = new wx.WebIM.message('txt', id); // 创建文本消息
  + 3、msg.set({                              
      msg: tsxtMsg,    // 消息体内容 
      to: roomId,      // 接收房间号
      from,            //发送方
      roomType: true,  // 通过roomType区分聊天室、群组，true为聊天室，false为群组
      ext: { nickName: this.data.nickName },  //扩展消息
      success: function (id, serverMsgId) {}, //成功后的回调
      fail: function (e) {} // 失败回调
    });
  + 4、msg.setGroup('groupchat') // 调用sdk接收成功的回调消息体
  + 5、wx.WebIM.conn.send(msg.body) // socket 发送
    ```
  >（详情请看demo中 sendTextMsg() 这个方法）

  ### demo中收到普通消息：
  + 在微信钩子函数中监听 onTextMessage 事件，通过sdk返回的成功回调，获取消息体。将消息体里的内容提取，通过this.setData()做消息上屏 
  >（详情请看 onTextMessage() 事件）

  ### demo中发送自定义消息（礼物、点赞消息等）
  ```
  + 1、let id = wx.WebIM.conn.getUniqueId() // 生成本地消息id
  + 2、let msg = new wx.WebIM.message('txt', id); // 创建文本消息
  + 3、
    msg.set({
      to: roomId,
      roomType: true,
      customEvent: 'chatroom_gift', // 自定义事件
      customExts: { note: self.data.giftModaData.giftName }, // 自定义消息扩展
      params: { id: 'gift_' + self.data.giftModaData.showGiftId, num: giftNum }, //自定义消息参数（这里展示的是礼物类别以及送的礼物数量）
      success: function () {},
      fail: function () {},
      ext: { nickName: self.data.nickName }
    })
  + 4、msg.setGroup('groupchat') // 调用sdk接收成功的回调消息体
  + 5、wx.WebIM.conn.send(msg.body) // socket 发送
  ```
  >（详情请看 sendGiftMsg()、giveLike()、sendSubtitles() 事件）

  ### demo中发送自定义消息（礼物、点赞消息等）
  + 与收到普通消息大同小异。在微信钩子函数中监听 onCustomMessage 事件，通过sdk返回的成功回调，来做数据处理
------
# 写在最后
第一期直播小程序demo完善了直播间聊天部分，主播开播下播、白名单用户、房间禁言等 [礼物、点赞类的实现的仅仅是静态示例。真正结合实际应用场景还需要用户根据自己的需求完善]

注意：这期我们没有加入用户系统、都是随机生成的。后续我们会引入直播视频、弹幕漂浮。让这款demo真正看起来符合一个开箱即用快速集成的直播小程序。

还有一些功能demo没有去实现但是sdk是支持的，要用到的话大家可以去查[文档](https://webim.easemob.com/sdk/jsdoc/out/connection.html) [当然也可以在环信提工单进行咨询]