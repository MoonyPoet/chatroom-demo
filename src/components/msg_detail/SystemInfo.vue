<template>
  <div class="sys-msg">
    <span v-text="info.text"></span>
  </div>
</template>

<script>
export default {
  name: 'SystemInfo',
  props: {
    info: {
      type: Object
    }
  },
  data () {
    return {
    }
  },
  methods: {
  }
}
</script>

<style scoped>
.sys-msg span {
  padding: .3rem 5rem;
  border-radius: .5rem;
  background-color: #CCCCCC;
  color: #FFFFFF;
  display: inline-block;
  margin-top: .5rem;
}
</style>
