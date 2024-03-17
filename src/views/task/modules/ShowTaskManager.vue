<!-- create j i s h e n g h u a -->
<template>
  <a-modal v-model="visible" title="详情" :width="1400">
    <template slot="footer">
      <a-button key="back" @click="handleCancel"  :loading="loading">
        取消
      </a-button>
      <!-- <a-button key="submit" type="primary" :loading="loading" @click="handleClick">
        确认
      </a-button> -->
    </template>
    <div :style="{display: 'flex',width: '20%',marginBottom: '10px'}">
      <a-button type="primary" @click="handleReport" :style="{marginRight: '10px'}">
        验收
      </a-button>
      <a-button type="primary" @click="handleInsert" :style="{marginRight: '10px'}">
        入库
      </a-button>
      <a-button type="primary" @click="handleComplete">
        完工
      </a-button>
    </div>
    <a-form layout="inline" @keyup.enter.native="taskSearchQuery">
      <a-row :gutter="24">
        <a-col :md="6" :sm="24">
          <a-form-item label="生产单号">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="商品条码">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="商品名称">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="多属性">
            <span>123</span>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :md="6" :sm="24">
          <a-form-item label="合计成本">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="订购数量">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="生产数量">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="销售订单号">
            <span>123</span>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :md="6" :sm="24">
          <a-form-item label="客户原始单号">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="计划完工">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="验收合格">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="已入库数">
            <span>123</span>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row :gutter="24">
        <a-col :md="6" :sm="24">
          <a-form-item label="实际完工">
            <span>123</span>
          </a-form-item>
        </a-col>
        <a-col :md="6" :sm="24">
          <a-form-item label="单据备注">
            <span>123</span>
          </a-form-item>
        </a-col>
      </a-row>
    </a-form>

    <a-tabs default-active-key="1">
        <a-tab-pane key="1" tab="1、所需物料" forceRender>
          <div :style="{display: 'flex',width: '20%',marginBottom: '10px'}">
            <a-button  @click="handleCancel" :style="{marginRight: '10px'}">
              批量领料
            </a-button>
            <a-button  @click="handleClick" :style="{marginRight: '10px'}">
              批量用料
            </a-button>
            <a-button @click="handleClick">
              生成采购订单
            </a-button>
          </div>

          <a-table
            :columns="columns1"
            :data-source="table1"
            :row-selection="rowSelection"
          >
          </a-table>
        </a-tab-pane>
        <a-tab-pane key="2" tab="1、生产工序" forceRender>
          <a-table
            :columns="columns2"
            :data-source="table2"
          >
          </a-table>
        </a-tab-pane>
        <a-tab-pane key="3" tab="1、验收记录" forceRender>
          <a-table
            :columns="columns3"
            :data-source="table3"
          >
          </a-table>
        </a-tab-pane>
    </a-tabs>

    <task-report ref="reportRef"></task-report>
    <insert-task ref="InsertRef"></insert-task>
  </a-modal>
</template>
<script>
  import Vue from 'vue'
  import TaskReport from "./TaskReport.vue"
  import InsertTask from './InsertTask.vue'
  // import { httpAction } from '@/api/manage'
  export default {
    name: "ShowTaskManager",
    components: {
      TaskReport,
      InsertTask,
    },
    data () {
      return {
        rowSelection: {
          selectedRowKeys: [], 
          onChange: this.onSelectChange
        },
        columns1: [
          {title: '操作',dataIndex: 'processesName', key: 'processesName'},
          {title: '条码',dataIndex: 'processesName', key: 'processesName'},
          {title: '名称',dataIndex: 'processesName', key: 'processesName'},
          {title: '规格',dataIndex: 'processesName', key: 'processesName'},
          {title: '型号',dataIndex: 'processesName', key: 'processesName'},
          {title: '库存',dataIndex: 'processesName', key: 'processesName'},
          {title: '单位',dataIndex: 'processesName', key: 'processesName'},
          {title: '多属性',dataIndex: 'processesName', key: 'processesName'},
          {title: '数量',dataIndex: 'processesName', key: 'processesName'},
          {title: '领料数',dataIndex: 'processesName', key: 'processesName'},
          {title: '已退数',dataIndex: 'processesName', key: 'processesName'},
          {title: '已用数',dataIndex: 'processesName', key: 'processesName'},
          {title: '报废数',dataIndex: 'processesName', key: 'processesName'},
          {title: '备注',dataIndex: 'processesName', key: 'processesName'},
        ],
        table1: [],
        columns2: [
          {title: '操作',dataIndex: 'processesName', key: 'processesName'},
          {title: '查看',dataIndex: 'processesName', key: 'processesName'},
          {title: '执行顺序',dataIndex: 'processesName', key: 'processesName'},
          {title: '工序名称',dataIndex: 'processesName', key: 'processesName'},
          {title: '负责人员',dataIndex: 'processesName', key: 'processesName'},
          {title: '工序状态',dataIndex: 'processesName', key: 'processesName'},
          {title: '计划完工时间',dataIndex: 'processesName', key: 'processesName'},
          {title: '备注',dataIndex: 'processesName', key: 'processesName'},
        ],
        table2: [],
        columns3: [
          {title: '验收日期',dataIndex: 'processesName', key: 'processesName'},
          {title: '验收人',dataIndex: 'processesName', key: 'processesName'},
          {title: '合格数量',dataIndex: 'processesName', key: 'processesName'},
          {title: '不合格数',dataIndex: 'processesName', key: 'processesName'},
          {title: '报废数量',dataIndex: 'processesName', key: 'processesName'},
          {title: '备注',dataIndex: 'processesName', key: 'processesName'},
        ],
        table3: [],
        visible: false,
        loading: false,
        taskId: '', // 任务id
      }
    },
    computed: {
    },
    created() {

    },
    methods: {
      // 验收
      handleReport() {
        this.$refs.reportRef.showModal(this.taskId)
      },
      // 入库
      handleInsert() {
        this.$refs.InsertRef.showModal(this.taskId)
      },
      onSelectChange(selectedRowKeys) {
        console.log('selectedRowKeys changed: ', selectedRowKeys);
        this.rowSelection.selectedRowKeys = selectedRowKeys;
      },
      showModal(id) {
        this.visible = true
        this.taskId = id
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