<!-- create j i s h e n g h u a -->
<template>
  <a-modal v-model="visible" title="验收" :width="800" destroyOnClose>
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
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="验收日期" data-step="1" data-title="验收日期"
                      data-intro="">
          <j-date  dateFormat='YYYY-MM-DD' v-decorator="['endTime']" :show-time="true"/>
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="验收人员" data-step="2" data-title="验收人员"
                      data-intro="">
            <a-select v-decorator.trim="[ 'userId' ]" placeholder="请选择验收人员">
              <a-select-option
                v-for="(item,index) in userList"
                :key="index"
                :value="item.value">
                {{ item.text || item.label }}
              </a-select-option>
            </a-select>
        </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="合格数量" data-step="3" data-title="合格数量"
                      data-intro="">
          <a-input placeholder="请输入合格数量" v-decorator.trim="[ 'okNumber' ]" />
        </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="不合格数量" data-step="4" data-title="不合格数量"
                      data-intro="">
          <a-input placeholder="请输入不合格数量" v-decorator.trim="[ 'lostNumber' ]" />
        </a-form-item>
        </a-col>
      </a-row>
       <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="报废数量" data-step="5" data-title="报废数量"
                      data-intro="">
          <a-input placeholder="请输入报废数量" v-decorator.trim="[ 'scrapNumber' ]" />
        </a-form-item>
        </a-col>
      </a-row>
       <a-row :gutter="24">
        <a-col :span="12">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="备注" data-step="6" data-title="备注"
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
  import { getUserList } from '@/api/api'
  import JDate from '@/components/jeecg/JDate'
  export default {
    name: "TaskReport",
    components: {
      JDate
    },
    data () {
      return {
        labelCol: {
          span: 6 
        },
        wrapperCol: {
          span: 18
        },
        form: this.$form.createForm(this),
        visible: false,
        loading: false,
        taskId: '',
        userList: []
      }
    },
    computed: {
    },
    created() {
      this.initUser()
    },
    methods: {
      initUser() {
        let that = this;
        getUserList({}).then((res)=>{
          if(res) {
            let arr = res
            for(let i=0; i<arr.length; i++) {
              let depotInfo = {};
              depotInfo.value = arr[i].id + '' //注意-此处value必须为字符串格式
              depotInfo.text = arr[i].userName
              depotInfo.title = arr[i].userName
              that.userList.push(depotInfo)
            }
          }
        });
      },
      showModal(id) {
        this.visible = true
        this.taskId = id
      },
      handleClick() {
        this.form.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values);
            values.taskId = this.taskId 
            values.checkUserId = values.userId
            httpAction('/taskReport/insertTaskReport', values, 'post').then((res) => {
              console.log(res, 'res');
              if(res.code === 200){
                this.visible = false
                this.$emit('ok', )
              }
            })
          }
        });
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