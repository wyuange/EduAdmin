<template>
  <div class="p_both10 p-t-5">
    <el-table
      ref="refElTabel"
      :data="customContractList"
      border
      tooltip-effect="light"
      style="width: 100%"
    >
      <el-table-column prop="Title" width="300" label="合同名称" :show-overflow-tooltip="true">
        <template slot-scope="scope">
          <el-tooltip
            class="item"
            effect="light"
            :content="'合同编号'+scope.row.TraceNo"
            placement="top-start"
          >
            <span
              class="color-1890ff font-w6 cursor"
              @click="editCustomContract(scope.$index, scope.row)"
            >{{ scope.row.Title }}</span>
          </el-tooltip>
        </template>
      </el-table-column>
      <el-table-column prop="CourseLabel" label="报读课程" :show-overflow-tooltip="true" />
      <el-table-column prop="ShijiPrice" width="120" label="实际价格(￥)" />
      <el-table-column prop="QiankuanPrice" width="100" label="欠款(￥)" />
    </el-table>
    <div class="m-v-15">
      <el-button type="primary" @click="addCustomContract">签订合同</el-button>
    </div>
    <!-- 合同弹出框 -->
 


    <custom-contract-dialog  :visible.sync="editDialog"
        width="600px" 
        :title="customRowData.Id>0?'编辑'+customRowData.Label:'新增'" ref="refContractDialog" @updateContractData="updateContractList" />
  </div>
</template>

<script>
import {
  getContractList,
  getCustomContract,
  addCustomContract,
  deleCustomContract,
  updateCustomContract
} from "@/api/contract";
import customContractDialog from "@/views/custom/component/customContractDialog";
import common from "@/utils/common";
export default {
  props: {
    // 校区的表单数据
    customData: {
      type: Object,
      default: function() {
        return { Id: 0 };
      }
    }
  },
  components: {
    customContractDialog
  },
  data() {
    return {
       // 更多操作弹窗
      moreOperationDialog: false,
      // 更多操作弹窗
      editDialog: false,
      common,
      // 客户合同信息列表
      customContractList: [],
      // 客户的个人信息
      customRowData: this.customData,
      // 当前操作合同的索引
      currentContractIndex: null
    };
  },

  mounted() { 
    // this.getContractList();
  },

  methods: {
    // 获取合同信息列表
    async getContractList() { 
      // 初始化数据
      this.customContractList = [];
      const res = await getCustomContract(this.customRowData.id);
      if (res.code == 200) {
        this.customContractList = res.data ? res.data : [];
      }
    },
    // 新增客户合同
    addCustomContract() {
      this.$refs.refContractDialog.getContractFormData(
        {
          StudentLabel: this.customRowData.Realname,
          StudentID: this.customRowData.id,
          YouhuiPrice: 0,
          QiankuanPrice: 0,
          Id: 0
        },
        2
      );
    },
    // 编辑合同表单的详情信息
    editCustomContract(index, row) {
      this.currentContractIndex = index;
      this.$refs.refContractDialog.getContractFormData(row, 0);
    },
    // 追加或编辑合同之后更新合同列表数据
    updateContractList(type, rowData) {
      // type=0编辑，type=1添加
      if (type == 1) {
        this.customContractList.unshift(rowData);
      } else if (type == 0) {
        this.customContractList.splice(this.currentContractIndex, 1, rowData);
      }
    }
  }
};
</script>
<style scoped>
</style>
