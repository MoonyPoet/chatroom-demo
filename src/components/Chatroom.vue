<template>
  <div>
    <div class='receiveMsg'>
      <msg-box
        v-for='(msg, index) in msgList'
        :msg='msg'
        :key='index'>
      </msg-box>
    </div>
    <div class="tools-bar">
      <button @click='pickPhoto'>图片</button>
      <input
        class='img-btn'
        type='file'
        accept='image/gif, image/jpeg, image/png'
        @change='insertImg'
      />
      <button @click='sendSysMsg'>系统消息</button>
    </div>
    <p class='edit-view' contenteditable='true'></p>
    <div class="buttom-bar">
      <button @click='sendMsg'>发送</button>
    </div>
  </div>
</template>

<script>
import MsgBox from '@/components/MsgBox'
export default {
  name: 'Chatroom',
  data () {
    return {
      // 消息列表
      msgList: [],
      // websocket对象
      websocket: null,
      // websocket链接状态
      isConnected: false
    }
  },
  components: {
    MsgBox
  },
  created () {
    const wcUrl = 'ws://meet.xpro.im:8080/xgate/websocket/123?nickname=POET&is_store_history=true'
    this.websocket = window.WebSocket || window.MozWebSocket

    // 初始化websocket
    if (window.WebSocket) {
      this.websocket = new WebSocket(wcUrl)
      this.websocket.onopen = this.doOpen
      this.websocket.onerror = this.doError
      this.websocket.onclose = this.doClose
      this.websocket.onmessage = this.doMessage
    } else {
      alert('您的设备不支持 webSocket!')
    }
  },
  methods: {
    doOpen () {
      this.isConnected = true
      console.log('连接成功')
    },
    doClose () {
      console.log('已经断开连接')
      this.isConnected = false
    },
    doError () {
      console.log('连接异常!')
      this.isConnected = false
    },
    // 接收websocket消息处理
    doMessage (message) {
      const data = JSON.parse(message.data)
      if (data.type === 'normal' || data.type === 'private') {
        let msg = JSON.parse(data.payload)

        // 测试先写死头像和昵称
        msg.info['avatar'] = 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1577619139562&di=e1d0e898cf2997cfbf095083b95e8782&imgtype=0&src=http%3A%2F%2Fcdn.duitang.com%2Fuploads%2Fitem%2F201412%2F17%2F20141217211542_CYRCV.jpeg'
        msg.info['name'] = data.from
        // 加入消息列表
        this.msgList.push(msg)
      }
    },
    // 插入图片
    insertImg (e) {
      let reader = new FileReader()

      reader.onload = () => {
        // 渲染图片至富文本
        document.execCommand('insertImage', false, reader.result)
        // 清空图片选择器
        document.querySelector('.img-btn').value = ''
      }

      reader.readAsDataURL(e.target.files[0])
    },
    // 发送消息
    sendMsg () {
      const msg = {
        type: 'user',
        info: {
          content: document.querySelector('.edit-view').innerHTML
        }
      }

      this.websocket.send(JSON.stringify(msg))
      // 清空富文本
      document.querySelector('.edit-view').innerHTML = ''
    },
    // 选择图片
    pickPhoto () {
      document.querySelector('.edit-view').focus()
      document.querySelector('.img-btn').click()
    },
    // 模拟发送系统消息
    sendSysMsg () {
      const msg = {
        type: 'system',
        info: {
          text: 'System Information!'
        }
      }

      this.websocket.send(JSON.stringify(msg))
    }
  }
}
</script>

<style scoped>
.img-btn {
  display: none;
}
.tools-bar {
  width: 400px;
  text-align: left;
  margin-left: 2rem;
  padding-top: 1rem;
}
.buttom-bar{
  width: 400px;
  text-align: right;
  margin-left: 2rem;
}
.edit-view {
  width: 400px;
  margin-left: 2rem;
  border: 1px solid;
  height: 100px;
  overflow: auto;
  text-align: left;
}
.receiveMsg {
  width: 400px;
  height: 400px;
  border: 1px solid;
  margin-left: 2rem;
  overflow-y: auto;
  padding-bottom: .5rem;
}
</style>
