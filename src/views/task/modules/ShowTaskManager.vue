<!-- create j i s h e n g h u a -->
<template>
  <a-modal v-model="visible" title="详情">
      <template slot="footer">
        <a-button key="back" @click="handleCancel"  :loading="loading">
          取消
        </a-button>
        <a-button key="submit" type="primary" :loading="loading" @click="handleClick">
          确认
        </a-button>
      </template>
    </a-modal>
</template>
<script>
  import Vue from 'vue'
  export default {
    name: "ShowTaskManager",
    components: {

    },
    data () {
      return {
        visible: false,
        loading: false,
      }
    },
    computed: {
    },
    created() {

    },
    methods: {
      showModal() {
        this.visible = true

      },
      handleClick() {
        this.visible = false
        this.$emit('ok', )
      },
      handleCancel() {
        this.visible = false
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>