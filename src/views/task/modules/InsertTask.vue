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
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="商品条码" data-step="1" data-title="商品条码"
                      data-intro="">
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="商品名称" data-step="1" data-title="商品名称"
                      data-intro="">
        </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="成本单价" data-step="1" data-title="成本单价"
                      data-intro="">
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="计量单位" data-step="1" data-title="计量单位"
                      data-intro="">
        </a-form-item>
        </a-col>
      </a-row>
       <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="当前库存" data-step="1" data-title="当前库存"
                      data-intro="">
          <a-input placeholder="请输入当前库存" v-decorator.trim="[ 'materialId' ]" :readOnly="true"/>
        </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="入库仓库" data-step="3" data-title="入库仓库"
                      data-intro="">
          <a-select placeholder="选择入库仓库" v-decorator.trim="[ 'depotId' ]"
              :dropdownMatchSelectWidth="false" showSearch optionFilterProp="children" allow-clear>
              <a-select-option v-for="(item,index) in depotList" :key="index" :value="item.id">
                {{ item.depotName }}
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="本次入库" data-step="1" data-title="本次入库"
                      data-intro="">
          <a-input placeholder="本次入库" v-decorator.trim="[ 'materialId' ]" />
        </a-form-item>
        </a-col>
      </a-row>
       <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="备注" data-step="1" data-title="备注"
                      data-intro="">
          <a-input placeholder="请输入备注" v-decorator.trim="[ 'remark' ]" />
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
    props: {
      depotList: {
        type: Array,
        default: () => []
      }
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