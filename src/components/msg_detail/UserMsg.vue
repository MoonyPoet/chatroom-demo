<template>
  <div class="user-msg">
    <div class="avatar" >
      <img :src="info.avatar" />
    </div>
    <div class="content" >
      <p v-text="info.name" />
      <span
        v-for="(msg, index) in msgList"
        :key="index">
        <span v-if="msg.type === 'text'" v-text="msg.text" />
        <img v-if="msg.type === 'img'" :src="msg.src" />
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'UserMsg',
  props: {
    info: {
      type: Object
    }
  },
  data () {
    return {
      // 消息内容临时字段
      contentTemp: '',
      // 消息内容列表
      msgList: []
    }
  },
  methods: {
    // 根据消息类型分类存储
    cutMsg () {
      // 取图片前文字
      let textBfImg = /.+?(?=<img)/.exec(this.contentTemp)
      // 取图片
      let img = /^<img src="(.*?)">/.exec(this.contentTemp)

      if (textBfImg) {
        this.msgList.push(
          {
            type: 'text',
            text: textBfImg[0]
          }
        )
        // 去除已分类消息内容
        this.contentTemp = this.contentTemp.replace(/.+?(?=<img)/, '')
        this.cutMsg()
      } else if (img) {
        this.msgList.push(
          {
            type: 'img',
            src: img[1]
          }
        )
        // 去除已分类消息内容
        this.contentTemp = this.contentTemp.replace(/^<img src="(.*?)">/, '')
        this.cutMsg()
      } else if (this.contentTemp.length > 0) { // 提取最后文本
        this.msgList.push(
          {
            type: 'text',
            text: this.contentTemp
          }
        )
      }
    }
  },
  created () {
    this.contentTemp = this.info.content
    this.cutMsg()
  }
}
</script>

<style scoped>
.user-msg {
  position: relative;
  text-align: left;
}
.avatar {
  display: inline-block;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 1px solid;
  overflow: hidden;
  margin: .5rem;
  vertical-align: top;
}
.content {
  display: inline-block;
  width: 300px;
  word-wrap: break-word;
  word-break: break-all;
}
.avatar img {
  width: 100%;
  height: 100%;
}
</style>
