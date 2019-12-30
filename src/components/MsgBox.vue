<template>
  <div>
    <component :is="comp" :ref="comp" :info="msg.info">
    </component>
  </div>
</template>

<script>
import UserMsg from '@/components/msg_detail/UserMsg'
import SysInfo from '@/components/msg_detail/SystemInfo'
export default {
  name: 'MsgBox',
  props: {
    msg: {
      type: Object
    }
  },
  data () {
    return {
    }
  },
  computed: {
    comp () {
      let compName = ''

      switch (this.msg.type) {
        case 'user':
          compName = 'UserMsg'
          break
        case 'system':
          compName = 'SysInfo'
          break
        default:
          break
      }
      return compName
    }
  },
  components: {
    UserMsg,
    SysInfo
  },
  methods: {
  }
}
</script>

<style scoped>
</style>
