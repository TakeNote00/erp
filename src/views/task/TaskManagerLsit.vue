<!-- create j i s h e n g h u a -->
<template>
  <a-row :gutter="24">
    <a-col :md="24">
      <a-card :style="cardStyle" :bordered="false">
        <!-- 查询区域 -->
        <div class="table-page-search-wrapper">
          <!-- 搜索区域 -->
          <a-form layout="inline" @keyup.enter.native="taskSearchQuery">
            <a-row :gutter="24">
              <a-col :md="6" :sm="24">
                <a-form-item label="生产单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
                  <a-input placeholder="请输入生产单号" v-model="queryParam.number"></a-input>
                </a-form-item>
              </a-col>
              <a-col :md="6" :sm="24">
                <a-form-item label="商品信息" :labelCol="labelCol" :wrapperCol="wrapperCol">
                  <a-input placeholder="请输入条码、名称、规格、型号" v-model="queryParam.key"></a-input>
                </a-form-item>
              </a-col>
              <a-col :md="6" :sm="24">
                <a-form-item label="单据日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
                  <a-range-picker
                    style="width:100%"
                    v-model="queryParam.createTimeRange"
                    format="YYYY-MM-DD"
                    :placeholder="['开始时间', '结束时间']"
                    @change="onDateChange"
                    @ok="onDateOk"
                  />
                </a-form-item>
              </a-col>
              <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                <a-col :md="6" :sm="24">
                  <a-button type="primary" @click="taskSearchQuery">查询</a-button>
                  <a-button style="margin-left: 8px" @click="searchReset">重置</a-button>
                  <a @click="handleToggleSearch" style="margin-left: 8px">
                    {{ toggleSearchStatus ? '收起' : '展开' }}
                    <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
                  </a>
                </a-col>
              </span>
              <template v-if="toggleSearchStatus">
                <a-col :md="6" :sm="24">
                  <a-form-item label="销售订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
                    <a-input placeholder="请输入销售订单号" v-model="queryParam.saleNo"></a-input>
                  </a-form-item>
                </a-col>
                <a-col :md="6" :sm="24">
                  <a-form-item label="计划完工" :labelCol="labelCol" :wrapperCol="wrapperCol">
                    <a-range-picker
                      style="width:100%"
                      v-model="queryParam.completeTimeRange"
                      format="YYYY-MM-DD"
                      :placeholder="['开始时间', '结束时间']"
                      @change="onCompleteDateChange"
                      @ok="onDateOk"
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="6" :sm="24">
                  <a-form-item label="单据状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
                    <a-select placeholder="选择单据状态" v-model="queryParam.status">
                      <a-select-option value="0">未审核</a-select-option>
                      <a-select-option value="1">已审核</a-select-option>
                      <a-select-option value="3">部分出库</a-select-option>
                      <a-select-option value="2">完成出库</a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="6" :sm="24">
                  <a-form-item label="客户原始单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
                    <a-input placeholder="请输入客户原始单号" v-model="queryParam.customerOriginalNo"></a-input>
                  </a-form-item>
                </a-col>
                <a-col :md="6" :sm="24">
                  <a-form-item label="单据备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
                    <a-input placeholder="请输入单据备注" v-model="queryParam.remark"></a-input>
                  </a-form-item>
                </a-col>
              </template>
            </a-row>
          </a-form>
        </div>
        <!-- 操作按钮区域 -->
        <div class="table-operator"  style="margin-top: 5px">
          <a-button v-if="btnEnableList.indexOf(1)==-1" @click="myHandleAdd" type="primary" icon="plus">新增</a-button>
          <a-button v-if="btnEnableList.indexOf(1)==-1" icon="delete" @click="batchDel">删除</a-button>
          <a-button v-if="checkFlag && btnEnableList.indexOf(2)==-1" icon="check" @click="batchSetStatus(1)">审核</a-button>
          <a-button v-if="checkFlag && btnEnableList.indexOf(7)==-1" icon="stop" @click="batchSetStatus(0)">反审核</a-button>
          <!-- <a-button v-if="isShowExcel && btnEnableList.indexOf(3)>-1" icon="download" @click="handleExport">导出</a-button> -->
          <a-popover trigger="click" placement="right">
            <template slot="content">
              <a-checkbox-group @change="onColChange" v-model="settingDataIndex" :defaultValue="settingDataIndex">
                <a-row style="width: 500px">
                  <template v-for="(item,index) in defColumns">
                    <template>
                      <a-col :span="8" :key="index">
                        <a-checkbox :value="item.dataIndex">
                          <j-ellipsis :value="item.title" :length="10"></j-ellipsis>
                        </a-checkbox>
                      </a-col>
                    </template>
                  </template>
                </a-row>
                <a-row style="padding-top: 10px;">
                  <a-col>
                    恢复默认列配置：<a-button @click="handleRestDefault" type="link" size="small">恢复默认</a-button>
                  </a-col>
                </a-row>
              </a-checkbox-group>
            </template>
            <a-button icon="setting">列设置</a-button>
          </a-popover>
          <a-tooltip placement="left" title="销售出库单可以由销售订单转过来，也可以单独创建。
          销售出库单据中的仓库列表只显示当前用户有权限的仓库。销售出库单可以使用多账户收款。
          勾选单据之后可以进行批量操作（删除、审核、反审核）" slot="action">
            <a-icon v-if="btnEnableList.indexOf(1)>-1" type="question-circle" style="font-size:20px;float:right;" />
          </a-tooltip>
        </div>
        <!-- table区域-begin -->
        <div>
          <a-table
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            :columns="columns"
            :dataSource="dataSource"
            :components="handleDrag(columns)"
            :pagination="ipagination"
            :scroll="scroll"
            :loading="loading"
            :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
            @change="handleTableChange">
            <span slot="action" slot-scope="text, record">
              <!-- <a @click="myHandleDetail(record, '销售出库', prefixNo)">查看</a> -->
              <a @click="handleOpenProcess(record)">查看</a>
              <a-divider v-if="btnEnableList.indexOf(1)==-1" type="vertical" />
              <a v-if="btnEnableList.indexOf(1)==-1" @click="myHandleEdit(record)">编辑</a>
              <a-divider v-if="btnEnableList.indexOf(1)==-1" type="vertical" />
              <a v-if="btnEnableList.indexOf(1)>-1" @click="myHandleCopyAdd(record)">复制</a>
              <a-divider v-if="btnEnableList.indexOf(1)>-1" type="vertical" />
              <a-popconfirm v-if="btnEnableList.indexOf(1)==-1" title="确定删除吗?" @confirm="() => myHandleDelete(record)">
                <a>删除</a>
              </a-popconfirm>
            </span>
            <template slot="customRenderDebt" slot-scope="value, record">
              <a-tooltip title="有收款单">
                <span style="color:green" v-if="value>0 && record.hasFinancialFlag">{{value}}</span>
              </a-tooltip>
              <a-tooltip title="暂未收款">
                <span style="color:red" v-if="value>0 && !record.hasFinancialFlag">{{value}}</span>
              </a-tooltip>
              <span v-if="value===0">{{value}}</span>
            </template>
            <template slot="customRenderStatus" slot-scope="status">
              <a-tag v-if="status == '0'" color="red">未审核</a-tag>
              <a-tag v-if="status == '1'" color="green">已审核</a-tag>
              <a-tag v-if="status == '2'" color="cyan">完成出库</a-tag>
              <a-tag v-if="status == '3'" color="blue">部分出库</a-tag>
              <a-tag v-if="status == '9'" color="orange">审核中</a-tag>
            </template>
          </a-table>
        </div>
        <!-- table区域-end -->
        <!-- 表单区域 -->
        <task-manager-modal ref="modalForm" @ok="modalFormOk" @close="modalFormClose"></task-manager-modal>
        <bill-detail ref="modalDetail" @ok="modalFormOk" @close="modalFormClose"></bill-detail>
        <bill-excel-iframe ref="billExcelIframe" @ok="modalFormOk" @close="modalFormClose"></bill-excel-iframe>
        <show-task-manager ref="showTaskManager" @ok="modalFormOk" @close="modalFormClose"></show-task-manager>
      </a-card>
    </a-col>
  </a-row>
</template>
<script>
  import TaskManagerModal from './modules/TaskManagerModal.vue'
  import ShowTaskManager from './modules/ShowTaskManager.vue'
  import BillDetail from '../bill/dialog/BillDetail'
  import BillExcelIframe from '@/components/tools/BillExcelIframe'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import { BillListMixin } from '../bill/mixins/BillListMixin'
  import JEllipsis from '@/components/jeecg/JEllipsis'
  import JDate from '@/components/jeecg/JDate'
  import Vue from 'vue'
  export default {
    name: "TaskManagerLsit",
    mixins:[JeecgListMixin,BillListMixin],
    components: {
      TaskManagerModal,
      BillDetail,
      BillExcelIframe,
      JEllipsis,
      JDate,
      ShowTaskManager
    },
    data () {
      return {
        // 查询条件
        queryParam: {
          number: "",
          key: "",
          type: "出库",
          subType: "销售",
          organId: "",
          depotId: "",
          creator: "",
          saleNo: "",
          accountId: "",
          hasDebt: "",
          status: "",
          remark: ""
        },
        prefixNo: 'XSCK',
        labelCol: {
          span: 5
        },
        wrapperCol: {
          span: 18,
          offset: 1
        },
        // 默认索引
        defDataIndex:['action','organName','number','materialsList','operTimeStr','userName','materialCount','totalPrice','totalTaxLastMoney',
          'needOutMoney','changeAmount','debt','status'],
        // 默认列
        defColumns: [
          {
            title: '操作',
            dataIndex: 'action',
            align:"center", width: 180,
            scopedSlots: { customRender: 'action' },
          },
          { title: '生产单号', dataIndex: 'produceNo',width:120},
          { title: '销售订单号', dataIndex: 'saleNo',width:140},
          { title: '商品条码', dataIndex: 'barCode',width:140},
          { title: '商品名称', dataIndex: 'goodsName',width:140},
          { title: '商品规格', dataIndex: 'standard',width:140},
          { title: '商品型号', dataIndex: 'model',width:140},
          { title: '订购数量', dataIndex: 'orderNumber',width:120},
          { title: '生产数量', dataIndex: 'produceNumber',width:120},
          { title: '计划完工日期', dataIndex: 'planFinishTimeStr',width:120},
          { title: '单据日期', dataIndex: 'createTimeStr',width:120},
          { title: '操作员', dataIndex: 'userName',width:80, ellipsis:true},
          { title: '状态', dataIndex: 'status', width: 80, align: "center",
            scopedSlots: { customRender: 'customRenderStatus' }
          }
        ],
        url: {
          list: "/task/list",
          delete: "/task/deleteByIds",
          deleteBatch: "/task/deleteByIds",
          batchSetStatusUrl: "/task/updateTask"
        }
      }
    },
    computed: {
    },
    created() {
      this.initSystemConfig()
      this.initCustomer()
      this.getDepotData()
      this.initUser()
      this.initAccount()
    },
    methods: {
      // 查看任务详情
      handleOpenProcess(record) {
        console.log(record, 'record');
        this.$refs.showTaskManager.showModal(record.id, this.depotList)
      },
      // 计划完工时间格式化
      onCompleteDateChange(value, dateString) {
        this.queryParam.planBeginTime = dateString[0];
        this.queryParam.planEndTime = dateString[1];
      },
      // 查询
      taskSearchQuery() {
        let filters = {
          planBeginTime: this.queryParam.planBeginTime,
          planEndTime: this.queryParam.planEndTime,
          beginTime: this.queryParam.beginTime,
          endTime: this.queryParam.endTime,
        }
        this.filters = Object.assign(this.filters, filters);
        this.searchQuery()
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>