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
    <a-form layout="inline">
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
            <a-button  @click="getMaterialBatch" :style="{marginRight: '10px'}">
              批量领料
            </a-button>
            <a-button  @click="useMaterialBatch" :style="{marginRight: '10px'}">
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
            <template slot="operation" slot-scope="text, record">
              <div :style="{display: 'flex',marginBottom: '10px'}">
                <a-button type="link"  @click="handleGetMaterial(record)" :style="{marginRight: '10px'}">
                  领料
                </a-button>
                <a-button type="link"  @click="handleBackMaterial(record)" :style="{marginRight: '10px'}">
                  退料
                </a-button>
                <a-button type="link" @click="handleUseMaterial(record)">
                  用料
                </a-button>
              </div>
            </template>
          </a-table>
        </a-tab-pane>
        <a-tab-pane key="2" tab="2、生产工序" forceRender>
          <a-table
            :columns="columns2"
            :data-source="table2"
          >
          </a-table>
        </a-tab-pane>
        <a-tab-pane key="3" tab="3、验收记录" forceRender>
          <a-table
            :columns="columns3"
            :data-source="table3"
          >
          
          </a-table>
        </a-tab-pane>
    </a-tabs>

    <task-report ref="reportRef"></task-report>
    <insert-task ref="InsertRef" :depotList="depotList"></insert-task>

    <!-- 领料，用料，退料 -->
    <a-modal v-model="materialVisible" :title="materialTitle" :width="800">
      <template slot="footer">
        <a-button key="back" @click="handleMaterialVisibleCancel">
          取消
        </a-button>
        <a-button key="submit" type="primary" @click="handleMaterialVisibleClick">
          确认
        </a-button>
      </template>
      <a-form :form="materialForm" :label-col="labelCol" :wrapper-col="wrapperCol">
        <a-form-item label="物料条码" data-step="1" data-title="物料条码"
                      data-intro="">
          <div>{{ materialRow.barCode }}</div>
        </a-form-item>
        <a-form-item label="物料名称" data-step="2" data-title="物料名称"
                      data-intro="">
          <div>{{ materialRow.name }}</div>
        </a-form-item>
        <div v-if="materialType == 0">
          <a-form-item label="物料仓库" data-step="3" data-title="物料仓库"
                      data-intro="">
          <a-select placeholder="选择仓库" v-decorator.trim="[ 'depotId' ]"
              :dropdownMatchSelectWidth="false" showSearch optionFilterProp="children" allow-clear>
              <a-select-option v-for="(item,index) in depotList" :key="index" :value="item.id">
                {{ item.depotName }}
              </a-select-option>
            </a-select>
          </a-form-item>
          <a-form-item label="当前库存" data-step="4" data-title="当前库存"
                          data-intro="">
            <a-input placeholder="请输入当前库存" v-decorator.trim="[ 'bb' ]" :readOnly="true" />
          </a-form-item>
          <a-form-item label="本次领料" data-step="5" data-title="本次领料"
                          data-intro="">
            <a-input placeholder="请输入本次领料" v-decorator.trim="[ 'getMaterial' ]" />
          </a-form-item>
          <a-form-item label="领料单位" data-step="6" data-title="领料单位"
                          data-intro="">
            <a-input placeholder="请输入领料单位" v-decorator.trim="[ 'unit' ]" :readOnly="true" />
          </a-form-item>
          <a-form-item label="成本单价" data-step="7" data-title="成本单价"
                          data-intro="">
            <a-input placeholder="请输入成本单价" v-decorator.trim="[ 'aa' ]" :readOnly="true" />
          </a-form-item>
        </div>
        <div v-if="materialType == 2">
          <a-form-item label="可用数量" data-step="1" data-title="可用数量"
                          data-intro="">
            <a-input placeholder="请输入可用数量" v-decorator.trim="[ 'number' ]" :readOnly="true" />
          </a-form-item>
          <a-form-item label="本次用料" data-step="2" data-title="本次用料"
                          data-intro="">
            <a-input placeholder="请输入本次用料" v-decorator.trim="[ 'useNumber' ]" />
          </a-form-item>
          <a-form-item label="本次废料" data-step="3" data-title="本次废料"
                          data-intro="">
            <a-input placeholder="请输入本次废料" v-decorator.trim="[ 'lostNumber' ]" />
          </a-form-item>
          <a-form-item label="单位" data-step="4" data-title="单位"
                          data-intro="">
            <a-input placeholder="请输入单位" v-decorator.trim="[ 'unit' ]" :readOnly="true" />
          </a-form-item>
        </div>
        <div v-if="materialType == 1">
          <a-form-item label="退料仓库" data-step="3" data-title="退料仓库"
                      data-intro="">
          <a-select placeholder="选择仓库" v-decorator.trim="[ 'depotId' ]"
              :dropdownMatchSelectWidth="false" showSearch optionFilterProp="children" allow-clear>
              <a-select-option v-for="(item,index) in depotList" :key="index" :value="item.id">
                {{ item.depotName }}
              </a-select-option>
            </a-select>
          </a-form-item>
          <a-form-item label="当前库存" data-step="4" data-title="当前库存"
                          data-intro="">
            <a-input placeholder="请输入当前库存" v-decorator.trim="[ 'bb' ]" :readOnly="true" />
          </a-form-item>
          <a-form-item label="可退数量" data-step="4" data-title="可退数量"
                          data-intro="">
            <a-input placeholder="请输入可退数量" v-decorator.trim="[ 'cc' ]" :readOnly="true" />
          </a-form-item>
          <a-form-item label="本次退料" data-step="5" data-title="本退领料"
                          data-intro="">
            <a-input placeholder="请输入本次退料" v-decorator.trim="[ 'operNumber' ]" />
          </a-form-item>
          <a-form-item label="退料单位" data-step="6" data-title="退料单位"
                          data-intro="">
            <a-input placeholder="请输入退料单位" v-decorator.trim="[ 'unit' ]" :readOnly="true" />
          </a-form-item>
          <a-form-item label="成本单价" data-step="7" data-title="成本单价"
                          data-intro="">
            <a-input placeholder="请输入成本单价" v-decorator.trim="[ 'aa' ]" :readOnly="true" />
          </a-form-item>
        </div>
      </a-form>
    </a-modal>

    <!-- 批量领料 -->
    <a-modal v-model="getMaterialVisible" title="批量领料【自动生成其它出库单】" :width="400">
      <template slot="footer">
        <a-button key="back" @click="() => getMaterialVisible = false">
          取消
        </a-button>
        <a-button key="submit" type="primary" @click="handleGetMaterialBatch">
          确认
        </a-button>
      </template>
      <a-form :form="materialForm" :label-col="labelCol" :wrapper-col="wrapperCol">
        <a-form-item label="领料仓库" data-step="1" data-title="领料仓库"
                  data-intro="">
          <a-select placeholder="选择仓库" v-decorator.trim="[ 'depotId' ]"
              :dropdownMatchSelectWidth="false" showSearch optionFilterProp="children" allow-clear>
              <a-select-option v-for="(item,index) in depotList" :key="index" :value="item.id">
                {{ item.depotName }}
              </a-select-option>
            </a-select>
        </a-form-item>
      </a-form>
      
    </a-modal>
  </a-modal>
</template>
<script>
  import Vue from 'vue'
  import TaskReport from "./TaskReport.vue"
  import InsertTask from './InsertTask.vue'
  import { httpAction } from '../../../api/manage'
  // import { httpAction } from '@/api/manage'
  export default {
    name: "ShowTaskManager",
    components: {
      TaskReport,
      InsertTask,
    },
    data () {
      return {
        depotList: [],
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 12 },
        },
        rowSelection: {
          selectedRowKeys: [], 
          selectedRows: [],
          onChange: this.onSelectChange
        },
        columns1: [
          {title: '操作',dataIndex: 'operation', key: 'operation',scopedSlots: { customRender: 'operation' },},
          {title: '条码',dataIndex: 'barCode', key: 'barCode'},
          // {title: '名称',dataIndex: 'processesName', key: 'processesName'},
          // {title: '规格',dataIndex: 'processesName', key: 'processesName'},
          // {title: '型号',dataIndex: 'processesName', key: 'processesName'},
          // {title: '库存',dataIndex: 'materialHasNumber', key: 'materialHasNumber'},
          // {title: '单位',dataIndex: 'processesName', key: 'processesName'},
          // {title: '多属性',dataIndex: 'processesName', key: 'processesName'},
          {title: '数量',dataIndex: 'materialHasNumber', key: 'materialHasNumber'},
          {title: '领料数',dataIndex: 'materialGetNumber', key: 'materialGetNumber'},
          {title: '已退数',dataIndex: 'materialReturnNumber', key: 'materialReturnNumber'},
          {title: '已用数',dataIndex: 'materialUseNumber', key: 'materialUseNumber'},
          {title: '报废数',dataIndex: 'materialLostNumber', key: 'materialLostNumber'},
          {title: '备注',dataIndex: 'remark', key: 'remark'},
        ],
        table1: [{id: '123213', barCode: '1001'}],
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
        materialRow: {},
        materialVisible: false,
        materialTitle: '',
        materialType: 0, // 0领料 1退料 2用料
        materialForm: this.$form.createForm(this),
        getMaterialVisible: false
      }
    },
    computed: {
    },
    created() {
      
    },
    watch: {
      visible() {
        if (this.visible) {
          // this.getMaterial()
          this.getTaskReport()
        }
      },
    },
    methods: {
      // 确定批量领料
      handleGetMaterialBatch() {
        let data = {
          taskMaterialList: this.rowSelection.selectedRows,
          depotId: this.materialForm.getFieldValue(depotId),
          taskId: this.taskId
        }
        httpAction('/taskMaterial/getMaterial', data, 'post').then((res) => {
          if(res.code === 200){
            this.getMaterialVisible = false
          }
        })
      },
      // 批量领料
      getMaterialBatch() {
        if (this.rowSelection.selectedRowKeys.length === 0) {
          this.$message.info('请选择一条记录')
        } else {
          this.getMaterialVisible = true
        }
      },
      // 批量用料
      useMaterialBatch() {
        if (this.rowSelection.selectedRowKeys.length === 0) {
          this.$message.info('请选择一条记录')
          
        } else {
          let that = this
          this.$confirm({
            title: '确认批量用料',
            content: h => <div style="">此操作执行完不可修改，确定要批量用料吗？</div>,
            onOk() {
              console.log('OK');
              let ids = []
              that.rowSelection.selectedRows.map((item) => {
                ids.push(item.id)
              })
              console.log(that.rowSelection.selectedRows, 'selectedRows');
              httpAction('/taskMaterial/useMaterial', ids, 'post').then((res) => {
                if(res.code === 200){
                  this.visible = false
                }
              })
            },
            onCancel() {
              console.log('Cancel');
            },
            class: 'test',
          });
        }
      },
      // 领料
      handleGetMaterial(row) {
        this.materialType = 0
        this.materialTitle = '领料【自动生成其它出库单】'
        this.materialRow = row
        this.materialForm.setFieldsValue({barCode: row.barCode})
        this.$nextTick(() => {
          this.materialVisible = true
        })
      },
      // 退料
      handleBackMaterial(row) {
        this.materialType = 1
        this.materialTitle = '退料【自动生成其它入库单】'
        this.materialRow = row
        this.materialForm.setFieldsValue({barCode: row.barCode})
        this.$nextTick(() => {
          this.materialVisible = true
        })
        
      },
      // 用料
      handleUseMaterial(row) {
        this.materialType = 2
        this.materialTitle = '用料'
        this.materialRow = row
        this.materialForm.setFieldsValue({barCode: row.barCode})
        this.$nextTick(() => {
          this.materialVisible = true
        })
        
      },
      handleMaterialVisibleCancel() {
        this.materialVisible = false
        this.materialForm.resetFields() // 重置表单
      },
      handleMaterialVisibleClick() {
        this.materialForm.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values);
            console.log('Received values of form: ', this.materialRow);
            // 领料
            if (this.materialType == 0) {
              let data = {
                  "taskMaterialList": [
                      {
                        "id": this.materialRow.id || '',
                        "materialEntity": {
                            "unit": this.materialRow.unit
                        }
                      }
                  ], //领料的材料id
                  "depotId": values.depotId, //仓库id
                  "taskId": this.taskId,
                  "getMaterial": values.getMaterial
              }
              httpAction('/taskMaterial/getMaterial', data, 'post').then((res) => {
                if(res.code === 200){
                  this.materialVisible = false
                  this.$message.info('领料成功')
                  this.materialForm.resetFields() // 重置表单
                }
              })
            }
            // 退料
            if (this.materialType == 1) {
              let data = {
                "depotId": values.depotId, //仓库id
                "taskId": this.taskId,
                "barCode": values.barCode,
                "unit": this.materialRow.unit,
                "operNumber": values.operNumber, 
              }
              httpAction('/task/warehousingProduct', data, 'post').then((res) => {
                if(res.code === 200){
                  this.materialVisible = false
                  this.$message.info('退料成功')
                  this.materialForm.resetFields() // 重置表单
                }
              })
            }
            // 用料
            if (this.materialType == 2) {
              let data = {
                "taskMaterialId": this.materialRow.id, 
                "useNumber": values.useNumber, 
                "lostNumber": values.lostNumber 
              }
              httpAction('/taskMaterial/useMaterialByNumber', data, 'post').then((res) => {
                if(res.code === 200){
                  this.materialVisible = false
                  this.$message.info('用料成功')
                  this.materialForm.resetFields() // 重置表单
                }
              })
            }
          }
        });
        
      },
      // 获取物料表格数据
      getMaterial() {
        let data = {
          "pageNo": 1, 
          "pageSize": 100, 
          "taskId": this.taskId 
        }
        httpAction('/taskMaterial/getListByPage', data, 'post').then((res) => {
          if(res.code === 200){
            this.table1 = res.data.data.records
          }
        })
      },
      // 验收
      handleReport() {
        this.$refs.reportRef.showModal(this.taskId)
      },
      // 入库
      handleInsert() {
        this.$refs.InsertRef.showModal(this.taskId)
      },
      // 完工
      handleComplete() {
        this.$confirm({
          title: '确认完工',
          content: h => <div style="">完工后如要修改需操作重新加工，确定完工吗？</div>,
          onOk() {
            console.log('OK');
            httpAction('/task/overTask', {taskId: this.taskId}, 'post').then((res) => {
              if(res.code === 200){
                this.$message.info('任务完工');
                this.visible = false
              }
            })
          },
          onCancel() {
            console.log('Cancel');
          },
          class: 'test',
        });
      },
      onSelectChange(selectedRowKeys, selectedRows) {
        console.log('selectedRowKeys changed: ', selectedRowKeys);
        this.rowSelection.selectedRowKeys = selectedRowKeys;
        this.rowSelection.selectedRows = selectedRows;
      },
      showModal(id, depotList) {
        this.depotList = depotList
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
      // 验收记录
      getTaskReport() {
        let data = {
          "taskId": this.taskId, 
          "processesId": null, 
          "pageNo": 1, 
          "pageSize": 100
        }
        httpAction('/taskReport/getListByPage', data, 'post').then((res) => {
            if(res.code === 200){
              this.tab3 = res.data.data.records
            }
          })
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>