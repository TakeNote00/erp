<!-- create j i s h e n g h u a -->
<template>
  <a-modal v-model="visible" title="入库【自动生成其它入库单】" :width="800" destroyOnClose>
    <template slot="footer">
      <a-button key="submit" type="primary" :loading="loading" @click="handleClick">
        确认
      </a-button>
      <a-button key="back" @click="handleCancel"  :loading="loading">
        取消
      </a-button>
    </template>
    
    <a-form :form="form">
      <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
      </a-row>
       <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
      </a-row>
       <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                      data-intro="">
          <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
      </a-row>
    </a-form>
  </a-modal>

  
</template>
<script>
  import Vue from 'vue'
  import { httpAction } from '@/api/manage'
  export default {
    name: "InsertTask",
    components: {

    },
    data () {
      return {
        goodsData: {

        },
        labelCol: {
          span: 6 
        },
        wrapperCol: {
          span: 18
        },
        form: this.$form.createForm(this),
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