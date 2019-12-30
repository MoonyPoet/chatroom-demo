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
      msgList: [],
      websocket: null,
      isConnected: false
    }
  },
  components: {
    MsgBox
  },
  created () {
    const wcUrl = 'ws://meet.xpro.im:8080/xgate/websocket/123?nickname=POET&is_store_history=true'
    this.websocket = window.WebSocket || window.MozWebSocket

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
    doMessage (message) {
      const data = JSON.parse(message.data)
      if (data.type === 'normal' || data.type === 'private') {
        console.log(data.payload)
        this.msgList.push(JSON.parse(data.payload))
      }
    },
    execCommand (name, args = null) {
      document.execCommand(name, false, args)
    },
    insertImg (e) {
      console.log(e)
      let reader = new FileReader()
      let file = e.target.files[0]

      reader.onload = () => {
        let base64Img = reader.result

        this.execCommand('insertImage', base64Img)
        document.querySelector('.img-btn').value = ''
      }

      reader.readAsDataURL(file)
    },
    sendMsg () {
      const msg = {
        type: 'user',
        info: {
          avatar: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1577619139562&di=e1d0e898cf2997cfbf095083b95e8782&imgtype=0&src=http%3A%2F%2Fcdn.duitang.com%2Fuploads%2Fitem%2F201412%2F17%2F20141217211542_CYRCV.jpeg',
          name: 'poet',
          content: document.querySelector('.edit-view').innerHTML
        }
      }
      this.websocket.send(JSON.stringify(msg))
      document.querySelector('.edit-view').innerHTML = ''
    },
    pickPhoto () {
      document.querySelector('.edit-view').focus()
      document.querySelector('.img-btn').click()
    },
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

<!-- Add 'scoped' attribute to limit CSS to this component only -->
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
