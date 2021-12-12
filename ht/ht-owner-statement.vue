<template>
  <div class="main-content">
    <el-row class="mb-10">
      <el-form :inline="true" size="mini" :model="searchParams" ref="searchParams" label-width="100px">
        <el-row>
          <el-col :span="6">
            <el-form-item label="合同甲方" prop="sPartyA">
              <el-input clearable v-model.trim="searchParams.sPartyA" style="width: 150px;"/>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="合同乙方" prop="sPartyBId">
              <el-select clearable filterable placeholder="合同乙方" v-model="searchParams.sPartyBId" style="width: 150px;">
                <el-option label="全部" value=""/>
                <el-option :key="index" :label="item.companyName" :value="item.id"
                           v-for="(item, index) in this.companyList"/>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="项目所属区域" prop="zoneId">
              <el-select clearable filterable placeholder="项目所属区域" v-model="searchParams.zoneId" style="width: 150px;">
                <el-option label="全部" value=""></el-option>
                <el-option :key="index" :label="item.name" :value="item.id"
                           v-for="(item, index) in configs.zones"/>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="项目简称" prop="buildingName">
              <el-input clearable v-model.trim="searchParams.buildingName" style="width: 150px;"/>
            </el-form-item>
          </el-col>
        </el-row>
        <el-collapse-transition>
          <div v-show="advanced">
            <el-row>
              <el-col :span="6">
                <el-form-item prop="sContractDateMin" label="签约日期开始">
                  <el-date-picker v-model="searchParams.sContractDateMin" type="date" placeholder="签约日期开始"
                                  value-format="yyyy-MM-dd" style="width: 150px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sContractDateMax" label="签约日期结束">
                  <el-date-picker v-model="searchParams.sContractDateMax" type="date" placeholder="签约日期结束"
                                  value-format="yyyy-MM-dd" style="width: 150px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sRentAreaMin" label="承租面积最小">
                  <el-input clearable placeholder="承租面积最小" type="number" size="mini" v-model.number="searchParams.sRentAreaMin"
                            style="width: 150px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sRentAreaMax" label="承租面积最大">
                  <el-input clearable placeholder="承租面积最大" size="mini" type="number" v-model.number="searchParams.sRentAreaMax"
                            style="width: 150px;"/>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="6">
                <el-form-item prop="sRentStartAt" label="起租日">
                  <el-date-picker v-model="searchParams.sRentStartAt" type="date" placeholder="起租日"
                                  value-format="yyyy-MM-dd" style="width: 150px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sRentEndAt" label="到期日">
                  <el-date-picker v-model="searchParams.sRentEndAt" type="date" placeholder="到期日"
                                  value-format="yyyy-MM-dd" style="width: 150px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sManagementIncluded" label="是否含管理费">
                  <el-select clearable placeholder="是否含管理费" v-model="searchParams.sManagementIncluded"
                             style="width: 150px;">
                    <el-option label="全部" value=""/>
                    <el-option label="是" value="1"/>
                    <el-option label="否" value="0"/>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sTaxIncluded" label="是否含税">
                  <el-select clearable placeholder="是否含税" v-model="searchParams.sTaxIncluded" style="width: 150px;">
                    <el-option label="全部" value=""/>
                    <el-option label="是" value="1"/>
                    <el-option label="否" value="0"/>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="6">
                <el-form-item label="是否备案" prop="sFiled">
                  <el-select clearable placeholder="是否备案" v-model="searchParams.sFiled" style="width: 150px;">
                    <el-option label="全部" value=""/>
                    <el-option label="是" value="1"/>
                    <el-option label="否" value="0"/>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sContractStateId" label="合同状态">
                  <el-select clearable placeholder="合同状态" v-model="searchParams.sContractStateId"
                             style="width: 150px;">
                    <el-option label="全部" value=""/>
                    <el-option v-for="item in configs.contractStates" :key="item.id" :label="item.name"
                               :value="item.id"/>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>
          </div>
        </el-collapse-transition>
        <div class="right-handle-group">
          <el-form-item>
            <el-button-group>
              <el-button @click="loadData(1)" type="primary" size="mini"
                         icon="el-icon-search">查询
              </el-button>
              <el-button @click="resetForm('searchParams')" type="primary" size="mini"
                         icon="el-icon-magic-stick">重置
              </el-button>
              <el-button @click="advanced = !advanced" type="primary" size="mini"
                         :icon="advanced ? 'el-icon-arrow-up' : 'el-icon-arrow-down'">{{
                  advanced ? '折叠查询条件' : '展开查询条件'
                }}
              </el-button>
            </el-button-group>
          </el-form-item>
        </div>
      </el-form>
    </el-row>
    <el-row>
      <el-table :data="dataList" border class="custom-table" size="mini" v-loading="loading">
        <div class="empty-wrap" slot="empty">
          <i class="iconfont icon-tishi"></i>
          <span>暂无数据</span>
        </div>
        <el-table-column align="center" label="序号" type="index" width="50px;"/>
        <el-table-column align="center" label="合同甲方(业主名称)" prop="partyA" width="200px;"/>
        <el-table-column align="center" label="甲方联系人" prop="aLinkman" width="100px;"/>
        <el-table-column align="center" label="甲方联系电话" prop="aContact" width="150px;"/>
        <el-table-column align="center" label="合同乙方(经营公司)" prop="partyB" width="220px;"/>
        <el-table-column align="center" label="项目所属区域" prop="zoneName" width="100px;"/>
        <el-table-column align="center" label="项目简称" prop="buildingName" width="100px;"/>
        <el-table-column align="center" label="项目地址" prop="address" width="300px;"/>
        <el-table-column align="center" label="合同编号" prop="contractNum" width="150px;"/>
        <el-table-column align="center" label="是否备案" prop="filed" width="100px;"/>
        <el-table-column align="center" label="租赁备案号" prop="rentFiledNum" width="150px;"/>
        <el-table-column align="center" label="签约日期" prop="contractDate" width="120px;"/>
        <el-table-column align="center" label="项目承租面积(m²)" prop="rentArea" width="120px;"/>
        <el-table-column align="center" label="承租单价(元/m²)" prop="rentPricePerSquare" width="120px;"/>
        <el-table-column align="center" label="月租金(元/月)" prop="monthlyFee" width="120px;"/>
        <el-table-column align="center" label="押金(元)" prop="deposits" width="100px;"/>
        <el-table-column align="center" label="租期(年)" prop="tenancyTerm" width="100px;"/>
        <el-table-column align="center" label="起租日" prop="rentStartAt" width="120px;"/>
        <el-table-column align="center" label="到期日" prop="rentEndAt" width="120px"/>
        <el-table-column align="center" label="免租期" prop="freeStartToEnd" width="240px;"/>
        <el-table-column align="center" label="管理费标准(元/m²/月)" prop="manPricePerSquare" width="160px;"/>
        <el-table-column align="center" label="电费标准(元/度)" prop="elePricePerDegree" width="120px;"/>
        <el-table-column align="center" label="水费标准(元/吨)" prop="waterPricePerTon" width="120px;"/>
        <el-table-column align="center" label="其它收费" width="260px;">
          <template slot-scope="scope">
            <p v-for='otherFee in scope.row.otherFeeStr'>{{ otherFee }}</p>
          </template>
        </el-table-column>
        <el-table-column align="center" label="是否含管理费" prop="managementIncluded" width="100px;"/>
        <el-table-column align="center" label="是否含税" prop="taxIncluded" width="100px;"/>
        <el-table-column align="center" label="递增明细" width="1000px;">
          <template slot-scope="scope">
            <p v-for='incr in scope.row.incrStr'>{{ incr }}</p>
          </template>
        </el-table-column>
        <el-table-column align="center" label="特殊条款" prop="specialTerms" width="200px;"/>
        <el-table-column align="center" label="合同状态" prop="contractState" width="100px;"/>
      </el-table>
      <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                     @current-change="changePage" @size-change="changeSize" background
                     layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
    </el-row>
  </div>
</template>

<script>
import api from 'api'

export default {
  name: '',
  data() {
    return {
      advanced: false,
      page: 1,
      pageSize: 10,
      dataList: [],
      companyList: [],
      totalSize: 10,
      loading: false,
      searchParams: {
        //报表查询条件
        sPartyA: "",//合同甲方（业主名称）
        sPartyBId: "",//合同乙方（经营公司）
        sZoneId: "",//项目所属区域
        buildingName: "",//项目简称
        sFiled: "",//是否备案
        sContractDateMin: "",//签约日期开始
        sContractDateMax: "",//签约日期结束
        sRentAreaMin: "",//项目承租面积最小
        sRentAreaMax: "",//项目承租面积最大
        sRentStartAt: "",//起租日
        sRentEndAt: "",//到期日
        sManagementIncluded: "",//是否含管理费
        sTaxIncluded: "",//是否含税
        sContractStateId: "",//合同状态
      },
    }
  },
  computed: {
    configs() {
      return this.$store.getters["sys/configs"];
    }
  },
  async mounted() {
    this.$store.dispatch('breadcrumb/clearPath');
    this.$store.dispatch('breadcrumb/addPath', {
      title: '报表管理/项目明细表',
      action: 'list-ht-owner-statement'
    });
    this.loadCompany();
    this.loadData();
  },
  methods: {
    /*hasPerms: function (perms) {
      // 根据权限标识和外部指示状态进行权限判断
      return hasPermission(perms)
    },*/
    changePage(pageNo) {
      this.page = pageNo;
      this.loadData();
    },
    changeSize(pageSize) {
      this.pageSize = pageSize;
      this.page = 1;
      this.loadData();
    },
    async loadData(page) {
      this.page = page ? page : this.page;
      this.loading = true;
      const params = {
        page: this.page,
        pageSize: this.pageSize,
      }
      Object.keys(this.searchParams).forEach(key => {
        params[key] = this.searchParams[key];
      })
      const result = await api.htOwnerContract.listStatement(params);
      this.loading = false;
      if (result.code === 200) {
        this.$nextTick(() => {
          this.dataList = result.data.records;
          this.dataList.forEach(item => {
            item.filed = item.filed !== (1 || '1') ? "否" : "是";
            item.address = (item.buildingProvince ? item.buildingProvince : "") + (item.buildingCity ? item.buildingCity : "") + (item.buildingAddress ? item.buildingAddress : "");
            item.managementIncluded = item.managementIncluded !== (1 || '1') ? "否" : "是";
            item.taxIncluded = item.taxIncluded !== (1 || '1') ? "否" : "是";
            item.incrStr = this.showIncrList(item.incrDetailsList);
            item.otherFeeStr = this.showOtherFee(item.otherFeeList);
            item.freeStartToEnd = item.freePeriod === 1 ? item.freeStartAt + " 至 " + item.freeEndAt : "无";
          })
          this.totalSize = result.data.total * 1;
        })
      } else {
        this.$message.error(result.msg);
      }
    },

    //合同乙方
    async loadCompany() {
      const result = await api.htOwnerContract.getCompany();
      if (result.code === 200) {
        this.companyList = result.data;
      } else {
        this.$message.error(result.msg);
      }
    },

    //重置搜索
    resetForm(searchParams) {
      this.$refs[searchParams].resetFields();
      this.loadData(1);
    },

    /**
     * 递增周期
     * @param incrList
     * @returns
     */
    showIncrList(incrList) {
      if (!incrList) {
        return "无";
      }
      if (incrList.length > 0) {
        let strList = [];
        incrList.forEach(item => {
          let str = "递增周期：" + item.incrPeriodEndAt + "至" + item.incrPeriodStartAt
            + ",递增比例：" + item.incrRatio + "%,递增单价：" + item.incrUnitPrice + "元/m²/月,递增金额："
            + item.incrFee + "元/月,递增后单价：" + item.afterIncrUnitPrice + "元/m²/月,递增后金额:"
            + item.afterIncrFee + "元/月";
          strList.push(str)
        })
        return strList
      } else {
        return "无";
      }
    },
    /**
     * 递增周期
     * @param fees
     * @returns
     */
    showOtherFee(fees) {
      if (!fees) {
        return "无";
      }
      if (fees.length > 0) {
        let strList = [];
        fees.forEach(item => {
          let str = "收费项目：" + item.name + ",金额：" + item.fee + "元";
          strList.push(str)
        })
        return strList
      } else {
        return "无";
      }
    },
  }
}
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {

  .el-table .cell {
    white-space: pre-line;
  }

  .right-handle-group {
    float: right;
  }
}
</style>
