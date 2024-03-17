<template>
  <j-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :keyboard="false"
    :forceRender="true"
    v-bind:prefixNo="prefixNo"
    switchHelp
    switchFullscreen
    @cancel="handleCancel"
    :id="prefixNo"
    style="top:20px;height: 95%;">
    <template slot="footer">
      <a-button @click="handleCancel">取消</a-button>
      <a-button v-if="checkFlag && isCanCheck" :loading="confirmLoading" @click="handleOkAndCheck">保存并审核</a-button>
      <a-button type="primary" :loading="confirmLoading" @click="handleClickOk">保存</a-button>
      <!--发起多级审核-->
      <a-button v-if="!checkFlag" @click="handleWorkflow()" type="primary">提交流程</a-button>
    </template>
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-row class="form-row" :gutter="24">
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产单号" data-step="1" data-title="生产单号"
                         data-intro="">
              <a-input placeholder="请输入生产单号" v-decorator.trim="[ 'produceNo' ]" :readOnly="true"/>
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="商品条码" data-step="2" data-title="商品条码"
              data-intro="">
              <a-input-search placeholder="请选择商品条码" v-decorator="[ 'barCode' ]" @search="onSearchLinkNumber" :readOnly="true"/>
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="商品名称" data-step="3" data-title="商品名称"
                         data-intro="">
              <a-input placeholder="请输入商品名称" v-decorator.trim="[ 'goodsName' ]" :readOnly="true"/>
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="计量单位" data-step="4" data-title="计量单位"
                         data-intro="">
              <a-input placeholder="请输入计量单位" v-decorator.trim="[ 'produceNo' ]" :readOnly="true"/>
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="订购数量" data-step="5" data-title="订购数量"
                         data-intro="">
              <a-input placeholder="请输入订购数量" v-decorator.trim="[ 'orderNumber' ]" />
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="计划完工">
              <j-date v-decorator="['planFinishTimeStr', validatorRules.planFinishTimeStr]" :show-time="true"/>
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="{xs: { span: 12},sm: { span: 12 }}" label="单据备注">
              <a-textarea :rows="1" placeholder="请输入备注" v-decorator="[ 'remark' ]"/>
            </a-form-item>
          </a-col>
          <a-col :lg="6" :md="12" :sm="24">
            <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产数量" data-step="8" data-title="生产数量"
                         data-intro="">
              <a-input placeholder="请输入生产数量" v-decorator.trim="[ 'produceNumber' ]" />
            </a-form-item>
          </a-col>
          <div v-show="false">
            <a-col :lg="6" :md="12" :sm="24">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产数量" data-step="8" data-title="生产数量"
                          data-intro="">
                <a-input placeholder="请输入生产数量" v-decorator.trim="[ 'number' ]" />
              </a-form-item>
            </a-col>
            <a-col :lg="6" :md="12" :sm="24">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产数量" data-step="8" data-title="生产数量"
                          data-intro="">
                <a-input placeholder="请输入生产数量" v-decorator.trim="[ 'operTime' ]" />
              </a-form-item>
            </a-col>
            <a-col :lg="6" :md="12" :sm="24">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="生产数量" data-step="8" data-title="生产数量"
                          data-intro="">
                <a-input placeholder="请输入生产数量" v-decorator.trim="[ 'accountId' ]" />
              </a-form-item>
            </a-col>
          </div>
        </a-row>
        <a-tabs default-active-key="1">
          <a-tab-pane key="1" tab="1、所需物料" forceRender>
            <j-editable-table id="billModal"
              :ref="refKeys[0]"
              :loading="materialTable.loading"
              :columns="materialTable.columns"
              :dataSource="materialTable.dataSource"
              :minWidth="minWidth"
              :maxHeight="300"
              :rowNumber="false"
              :rowSelection="rowCanEdit"
              :actionButton="rowCanEdit"
              :dragSort="rowCanEdit"
              @valueChange="onValueChange"
              @added="onAdded"
              @deleted="onDeleted">
              <template #buttonAfter>
                
              </template>
              <template #depotBatchSet>
                <a-icon type="down" @click="handleBatchSetDepot" />
              </template>
              <template #depotAdd>
                <a-divider v-if="isTenant" style="margin: 4px 0;" />
                <div v-if="isTenant" style="padding: 4px 8px; cursor: pointer;" @click="addDepot"><a-icon type="plus" /> 新增仓库</div>
              </template>
            </j-editable-table>
          </a-tab-pane>
          <a-tab-pane key="2" tab="2、生产工序" forceRender>
            <!-- <j-editable-table id="billModal"
              :ref="refKeys[1]"
              :loading="productionProcessTable.loading"
              :columns="productionProcessTable.columns"
              :dataSource="productionProcessTable.dataSource"
              :minWidth="minWidth"
              :maxHeight="300"
              :rowNumber="false"
              :rowSelection="rowCanEdit"
              :actionButton="rowCanEdit"
              :dragSort="rowCanEdit"
              @valueChange="onValueChange"
              @added="onAdded"
              @deleted="onDeleted">
              <template #buttonAfter>
                
              </template>
              <template #depotBatchSet>
                <a-icon type="down" @click="handleBatchSetDepot" />
              </template>
              <template #depotAdd>
                <a-divider v-if="isTenant" style="margin: 4px 0;" />
                <div v-if="isTenant" style="padding: 4px 8px; cursor: pointer;" @click="addDepot"><a-icon type="plus" /> 新增仓库</div>
              </template>
            </j-editable-table> -->
            <div :style="{display: 'flex',width: '20%',marginBottom: '10px'}">
              <a-button type="primary" @click="handleAddPorcess" :style="{marginRight: '10px'}">新增</a-button>
              <a-button v-show="this.rowSelection.selectedRowKeys.length === 1" @click="handleDelPorcess">删除</a-button>
              <!-- <a-button @click="handleEditPorcess">编辑</a-button> -->
            </div>
            
            <a-table
              :columns="tableColumns"
              :data-source="tableData"
              :row-selection="rowSelection"
            />
          </a-tab-pane>
        </a-tabs>
      </a-form>
    </a-spin>
    <many-account-modal ref="manyAccountModalForm" @ok="manyAccountModalFormOk"></many-account-modal>
    <import-item-modal ref="importItemModalForm" @ok="importItemModalFormOk"></import-item-modal>
    <link-bill-list ref="linkBillList" @ok="linkBillListOk"></link-bill-list>
    <customer-modal ref="customerModalForm" @ok="customerModalFormOk"></customer-modal>
    <depot-modal ref="depotModalForm" @ok="depotModalFormOk"></depot-modal>
    <account-modal ref="accountModalForm" @ok="accountModalFormOk"></account-modal>
    <batch-set-depot ref="batchSetDepotModalForm" @ok="batchSetDepotModalFormOk"></batch-set-depot>
    <history-bill-list ref="historyBillListModalForm"></history-bill-list>
    <workflow-iframe ref="modalWorkflow"></workflow-iframe>

    <!-- 新增工序 -->
    <a-modal v-model="taskProcessVisible" title="新增">
      <template slot="footer">
        <a-button key="back" @click="handletaskProcessVisibleCancel"  :loading="taskProcessLoading">
          取消
        </a-button>
        <a-button key="submit" type="primary" :loading="taskProcessLoading" @click="handletaskProcessVisibleOk">
          确认
        </a-button>
      </template>
      <a-form :form="taskProcessForm">
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="工序名称" data-step="1" data-title="工序名称"
                         data-intro="">
          <a-input placeholder="请输入工序名称" v-decorator.trim="[ 'processesName' ]" />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="负责人员" data-step="2" data-title="负责人员"
                         data-intro="">
          <a-input placeholder="请输入负责人员" v-decorator.trim="[ 'userId' ]" />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="父工序" data-step="3" data-title="父工序"
                         data-intro="">
          <a-input placeholder="请输入父工序" v-decorator.trim="[ 'parentProcesses' ]" />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="前序工序" data-step="4" data-title="前序工序"
                         data-intro="">
          <a-input placeholder="请输入前序工序" v-decorator.trim="[ 'beforeProcesses' ]" />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="计划完工日期" data-step="5" data-title="计划完工日期"
                         data-intro="">
          <a-input placeholder="请输入计划完工日期" v-decorator.trim="[ 'planOverTime' ]" />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="备注" data-step="6" data-title="备注"
                         data-intro="">
          <a-input placeholder="请输入备注" v-decorator.trim="[ 'remark' ]" />
        </a-form-item>
      </a-form>
    </a-modal>
  </j-modal>
</template>
<script>
  import pick from 'lodash.pick'
  import ManyAccountModal from '../../bill/dialog/ManyAccountModal'
  import ImportItemModal from '../../bill/dialog/ImportItemModal'
  import LinkBillList from '../../bill/dialog/LinkBillList'
  import CustomerModal from '../../system/modules/CustomerModal'
  import DepotModal from '../../system/modules/DepotModal'
  import AccountModal from '../../system/modules/AccountModal'
  import BatchSetDepot from '../../bill/dialog/BatchSetDepot'
  import HistoryBillList from '../../bill/dialog/HistoryBillList'
  import WorkflowIframe from '@/components/tools/WorkflowIframe'
  import { FormTypes } from '@/utils/JEditableTableUtil'
  import { JEditableTableMixin } from '@/mixins/JEditableTableMixin'
  import { BillModalMixin } from '../../bill/mixins/BillModalMixin'
  import { getMpListShort,handleIntroJs } from "@/utils/util"
  import JSelectMultiple from '@/components/jeecg/JSelectMultiple'
  import JUpload from '@/components/jeecg/JUpload'
  import JDate from '@/components/jeecg/JDate'
  import Vue from 'vue'
  import { VALIDATE_NO_PASSED, getRefPromise, validateFormAndTables } from '@/utils/JEditableTableUtil'
  import { httpAction } from '@/api/manage'
  import {addDepot,editDepot,checkDepot,getUserList } from '@/api/api'
  
  export default {
    name: "TaskManagerModal",
    mixins: [JEditableTableMixin, BillModalMixin],
    components: {
      ManyAccountModal,
      ImportItemModal,
      LinkBillList,
      CustomerModal,
      DepotModal,
      AccountModal,
      BatchSetDepot,
      HistoryBillList,
      WorkflowIframe,
      JUpload,
      JDate,
      JSelectMultiple,
      VNodes: {
        functional: true,
        render: (h, ctx) => ctx.props.vnodes,
      }
    },
    data () {
      return {
        tableColumns: [
          {title: '工序名称',dataIndex: 'processesName'},
          {title: '负责人员',dataIndex: 'userId'},
          {title: '父工序',dataIndex: 'parentProcesses'},
          {title: '前序工序',dataIndex: 'beforeProcesses'},
          {title: '计划完工日期',dataIndex: 'planOverTime'},
          {title: '备注',dataIndex: 'remark'},
        ],
        tableData: [],
        rowSelection: {
          selectedRowKeys: [], 
          onChange: this.onSelectChange
        },
        taskProcessForm: this.$form.createForm(this),
        taskProcessVisible: false,
        taskProcessLoading: false,
        title:"操作",
        width: '1600px',
        moreStatus: false,
        // 新增时子表默认添加几行空数据
        addDefaultRowNum: 1,
        visible: false,
        operTimeStr: '',
        prefixNo: 'XSCK',
        depositStatus: false,
        fileList:[],
        rowCanEdit: true,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 8 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        refKeys: ['materialDataTable'],
        activeKey: 'materialDataTable',
        productionProcessTable: {
          loading: false,
          dataSource: [],
          columns: [
            { title: '执行顺序', key: 'beforeProcesses', width: '15%', type: FormTypes.input },
            { title: '工序名称', key: 'processesName', width: '15%', type: FormTypes.input },
            { title: '负责人员', key: 'userId', width: '15%', type: FormTypes.select, 
          allowSearch:true, options: [],
          },
            { title: '计划完工时间', key: 'planOverTime', width: '15%', type: FormTypes.date },
            { title: '备注', key: 'remark', width: '15%', type: FormTypes.input },
          ]
        },
        materialTable: {
          loading: false,
          dataSource: [],
          columns: [
            // { title: '仓库名称', key: 'depotId', width: '8%', type: FormTypes.select, placeholder: '请选择${title}', options: [],
            //   allowSearch:true, validateRules: [{ required: true, message: '${title}不能为空' }]
            // },
            
            { title: '条码', key: 'barCode', width: '12%', type: FormTypes.popupJsh, kind: 'material', multi: true,
              validateRules: [{ required: true, message: '${title}不能为空' }]
            },
            { title: '型号', key: 'model', width: '9%', type: FormTypes.normal },
            { title: '名称', key: 'name', width: '10%', type: FormTypes.normal },
            { title: '颜色', key: 'color', width: '5%', type: FormTypes.normal },
            { title: '扩展信息', key: 'materialOther', width: '5%', type: FormTypes.normal },
            { title: '库存', key: 'stock', width: '5%', type: FormTypes.normal },
            { title: '单位', key: 'unit', width: '4%', type: FormTypes.normal },
            { title: '序列号', key: 'snList', width: '12%', type: FormTypes.popupJsh, kind: 'sn', multi: true },
            { title: '批号', key: 'batchNumber', width: '7%', type: FormTypes.popupJsh, kind: 'batch', multi: false },
            { title: '有效期', key: 'expirationDate',width: '7%', type: FormTypes.input, readonly: true },
            { title: '多属性', key: 'sku', width: '9%', type: FormTypes.normal },
            { title: '原数量', key: 'preNumber', width: '4%', type: FormTypes.normal },
            { title: '已出库', key: 'finishNumber', width: '4%', type: FormTypes.normal },
            { title: '数量', key: 'operNumber', width: '4%', type: FormTypes.inputNumber, statistics: true,
              validateRules: [{ required: true, message: '${title}不能为空' }]
            },
            { title: '单价', key: 'unitPrice', width: '4%', type: FormTypes.inputNumber},
            { title: '金额', key: 'allPrice', width: '5%', type: FormTypes.inputNumber, statistics: true },
            // { title: '税率', key: 'taxRate', width: '4%', type: FormTypes.inputNumber,placeholder: '%'},
            // { title: '税额', key: 'taxMoney', width: '5%', type: FormTypes.inputNumber, readonly: true, statistics: true },
            // { title: '价税合计', key: 'taxLastMoney', width: '7%', type: FormTypes.inputNumber, statistics: true },
            { title: '备注', key: 'remark', width: '6%', type: FormTypes.input },
            { title: '关联id', key: 'linkId', width: '5%', type: FormTypes.hidden },
          ]
        },
        confirmLoading: false,
        validatorRules:{
          operTime:{
            rules: [
              { required: true, message: '请输入单据日期！' }
            ]
          },
          organId:{
            rules: [
              { required: true, message: '请选择客户！' }
            ]
          },
          accountId:{
            rules: [
              { required: true, message: '请选择结算账户！' }
            ]
          },
          changeAmount:{
            rules: [
              { required: true, message: '请输入金额，如果为空请填0！' },
              { pattern: /^(([0-9][0-9]*)|([0]\.\d{0,4}|[0-9][0-9]*\.\d{0,4}))$/, message: '金额格式不正确!' }
            ]
          }
        },
        url: {
          add: '/task/insertTask',
          edit: '/task/updateTask',
          detailList: '/depotItem/getDetailList'
        }
      }
    },
    created () {
      this.initUser()
    },
    methods: {
      onSelectChange(selectedRowKeys) {

        console.log('selectedRowKeys changed: ', selectedRowKeys);
        this.rowSelection.selectedRowKeys = selectedRowKeys;
      },
      handleAddPorcess() {
        this.taskProcessVisible = true
      },
      handleDelPorcess() {
        this.rowSelection.selectedRowKeys.map((item) => {
          this.tableData.splice(Number(item), 1)
          this.rowSelection.selectedRowKeys = []
        })
      },
      handleEditPorcess() {

      },
      handletaskProcessVisibleCancel() {
        this.taskProcessVisible = false
      },
      handletaskProcessVisibleOk() {
        this.taskProcessLoading = true;
        console.log(this.taskProcessForm, '231');
        this.taskProcessForm.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values);
            setTimeout(() => {
              this.tableData.push(values)
              this.taskProcessVisible = false;
              this.taskProcessLoading = false;
              this.$nextTick(() => {
                this.taskProcessForm.setFieldsValue({
                  "processesName": "", 
                  "parentProcesses": "", 
                  "beforeProcesses": "", 
                  "planOverTime": "", 
                  "userId": "", 
                  "remark": "" 
                })
              });
            }, 2000);
          }
        });
        
      },
      initUser() {
        let that = this;
        getUserList({}).then((res)=>{
          if(res) {
            let arr = res
            for(let item of that.productionProcessTable.columns){
              if(item.key == 'userId') {
                item.options = []
                for(let i=0; i<arr.length; i++) {
                  let depotInfo = {};
                  depotInfo.value = arr[i].id + '' //注意-此处value必须为字符串格式
                  depotInfo.text = arr[i].userName
                  depotInfo.title = arr[i].userName
                  item.options.push(depotInfo)
                }
              }
            }
          }
        });
      },
      /** 确定按钮点击事件 */
      handleClickOk() {
        /** 触发表单验证 */
        this.getAllTable().then(tables => {
          /** 一次性验证主表和所有的次表 */
          return validateFormAndTables(this.form, tables)
        }).then(allValues => {
          if (typeof this.classifyFormData !== 'function') {
            throw this.throwNotFunction('classifyFormData')
          }
          if (this.tableData.length === 0) {
            this.$message.warning('请添加工序');
            return
          }
          console.log(allValues, 'allValues');
          let formData = this.classifyFormData(allValues)
          // 发起请求
          return this.httpRequest(formData)
        }).catch(e => {
          if (e.error === VALIDATE_NO_PASSED) {
            // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
            this.activeKey = e.index == null ? this.activeKey : this.refKeys[e.index]
          } else {
            console.error(e)
          }
        })
      },
      // 表单验证
      classifyFormData(allValues) {
        let obj = {
          "billNo": "1111111", 
          "materialId": 621, 
          "planOverTime": allValues.formValue.planFinishTimeStr, 
          "quantity": allValues.formValue.produceNumber, 
          "overQquantity": allValues.formValue.orderNumber, 
          "remark": allValues.formValue.remark,
          taskMaterialList: [],
          taskProcessesList: []
        }
        obj.taskProcessesList = this.tableData
        console.log(allValues, 'allValues');
        return obj
      },
      /** 发起请求，自动判断是执行新增还是修改操作 */
    httpRequest(formData) {
      let url = this.url.add, method = 'post'
      if (this.model.id) {
        url = this.url.edit
        method = 'put'
      }
      this.confirmLoading = true
      httpAction(url, formData, method).then((res) => {
        if(res.code === 200){
          this.$emit('ok')
          this.confirmLoading = false
          this.close()
        } else {
          this.$message.warning(res.data.message);
          this.confirmLoading = false
        }
      }).finally(() => {
      })
    },
      //调用完edit()方法之后会自动调用此方法
      editAfter() {
        this.billStatus = '0'
        this.currentSelectDepotId = ''
        this.rowCanEdit = true
        this.materialTable.columns[1].type = FormTypes.popupJsh
        this.changeColumnHide()
        this.changeFormTypes(this.materialTable.columns, 'snList', 0)
        this.changeFormTypes(this.materialTable.columns, 'batchNumber', 0)
        this.changeFormTypes(this.materialTable.columns, 'expirationDate', 0)
        this.changeFormTypes(this.materialTable.columns, 'preNumber', 0)
        this.changeFormTypes(this.materialTable.columns, 'finishNumber', 0)
        if (this.action === 'add') {
          this.depositStatus = false
          this.addInit(this.prefixNo)
          this.personList.value = ''
          this.fileList = []
          this.$nextTick(() => {
            handleIntroJs(this.prefixNo, 1)
          })
        } else {
          if(this.model.linkNumber) {
            this.rowCanEdit = false
            this.materialTable.columns[1].type = FormTypes.normal
          }
          this.model.operTime = this.model.operTimeStr
          if(this.model.deposit) {
            this.depositStatus = true
          } else {
            this.depositStatus = false
            this.model.deposit = 0
          }
          this.model.debt = (this.model.discountLastMoney + this.model.otherMoney - this.model.deposit - this.model.changeAmount).toFixed(2)
          if(this.model.accountId == null) {
            this.model.accountId = 0
            this.manyAccountBtnStatus = true
            this.accountIdList = this.model.accountIdList
            this.accountMoneyList = this.model.accountMoneyList
          } else {
            this.manyAccountBtnStatus = false
          }
          this.personList.value = this.model.salesMan
          this.fileList = this.model.fileName
          this.$nextTick(() => {
            this.form.setFieldsValue(pick(this.model,'organId', 'operTime', 'number', 'linkNumber', 'remark',
              'discount','discountMoney','discountLastMoney','otherMoney','accountId','deposit','changeAmount','debt','salesMan'))
          });
          // 加载子表数据
          let params = {
            headerId: this.model.id,
            mpList: getMpListShort(Vue.ls.get('materialPropertyList')),  //扩展属性
            linkType: 'basic'
          }
          let url = this.readOnly ? this.url.detailList : this.url.detailList;
          this.requestSubTableData(url, params, this.materialTable);
        }
        //复制新增单据-初始化单号和日期
        if(this.action === 'copyAdd') {
          this.model.id = ''
          this.model.tenantId = ''
          this.copyAddInit(this.prefixNo)
        }
        this.initSystemConfig()
        this.initCustomer()
        this.initSalesman()
        this.initDepot()
        this.initAccount()
      },
      //提交单据时整理成formData
      classifyIntoFormData(allValues) {
        let totalPrice = 0
        let billMain = Object.assign(this.model, allValues.formValue)
        let detailArr = allValues.tablesValue[0].values
        billMain.type = '出库'
        billMain.subType = '销售'
        billMain.defaultNumber = billMain.number
        for(let item of detailArr){
          totalPrice += item.allPrice-0
        }
        billMain.totalPrice = totalPrice
        if(billMain.accountId === 0) {
          billMain.accountId = ''
        }
        billMain.accountIdList = this.accountIdList.length>0 ? JSON.stringify(this.accountIdList) : ""
        billMain.accountMoneyList = this.accountMoneyList.length>0 ? JSON.stringify(this.accountMoneyList) : ""
        if(this.fileList && this.fileList.length > 0) {
          billMain.fileName = this.fileList
        } else {
          billMain.fileName = ''
        }
        if(this.model.id){
          billMain.id = this.model.id
        }
        billMain.salesMan = this.personList.value
        billMain.status = this.billStatus
        return {
          info: JSON.stringify(billMain),
          rows: JSON.stringify(detailArr),
        }
      },
      handleHistoryBillList() {
        let organId = this.form.getFieldValue('organId')
        this.$refs.historyBillListModalForm.show('出库', '销售', '客户', organId);
        this.$refs.historyBillListModalForm.disableSubmit = false;
      },
      onSearchLinkNumber() {
        this.$refs.linkBillList.show('其它', '销售订单', '客户', "1,3")
        this.$refs.linkBillList.title = "选择销售订单"
      },
      linkBillListOk(selectBillDetailRows, linkNumber, organId, discountMoney, deposit, remark, depotId) {
        let that = this
        this.rowCanEdit = false
        this.materialTable.columns[1].type = FormTypes.normal
        this.changeFormTypes(this.materialTable.columns, 'preNumber', 1)
        this.changeFormTypes(this.materialTable.columns, 'finishNumber', 1)
        if(selectBillDetailRows && selectBillDetailRows.length>0) {
          let listEx = []
          let allTaxLastMoney = 0
          for(let j=0; j<selectBillDetailRows.length; j++) {
            let info = selectBillDetailRows[j];
            if(info.finishNumber>0) {
              info.operNumber = info.preNumber - info.finishNumber
              info.allPrice = info.operNumber * info.unitPrice-0
              let taxRate = info.taxRate-0
              info.taxMoney = (info.allPrice*taxRate/100).toFixed(2)-0
              info.taxLastMoney = (info.allPrice + info.taxMoney).toFixed(2)-0
            }
            info.linkId = info.id
            allTaxLastMoney += info.taxLastMoney
            listEx.push(info)
            this.changeColumnShow(info)
          }
          this.materialTable.dataSource = listEx
          ///给优惠后金额重新赋值
          allTaxLastMoney = allTaxLastMoney?allTaxLastMoney:0
          let discount = 0
          if(allTaxLastMoney!==0) {
            discount = (discountMoney/allTaxLastMoney*100).toFixed(2)-0
          }
          let discountLastMoney = (allTaxLastMoney - discountMoney).toFixed(2)-0
          let changeAmount = discountLastMoney
          if(deposit) {
            this.depositStatus = true
            changeAmount = (discountLastMoney - deposit).toFixed(2)-0
          }
          this.$nextTick(() => {
            this.form.setFieldsValue({
              'organId': organId,
              'linkNumber': linkNumber,
              'discount': discount,
              'discountMoney': discountMoney,
              'discountLastMoney': discountLastMoney,
              'deposit': deposit,
              'changeAmount': changeAmount,
              'remark': remark
            })
          })
          //判断后进行仓库的切换
          if(depotId) {
            setTimeout(function () {
              that.batchSetDepotModalFormOk(depotId)
            },1000)
          }
        }
      },
    }
  }
</script>
<style scoped>

</style>