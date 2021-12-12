<template>
  <div class="main-content">
    <el-row>
      <el-form size="mini" :model="searchParams" ref="searchParams" label-width="120px">
        <el-row>
          <el-col :span="6">
            <el-form-item prop="sRoomNum" label="房号">
              <el-input clearable placeholder="房号" size="mini" v-model.trim="searchParams.sRoomNum" style="width: 170px;"/>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item prop="sTenantName" label="承租公司">
              <el-input clearable placeholder="承租公司" size="mini" v-model.trim="searchParams.sTenantName"
                        style="width: 170px;"/>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item prop="sManagementTypeId" label="业务类型">
              <el-select clearable placeholder="业务类型" v-model="searchParams.sManagementTypeId" style="width: 170px;">
                <el-option label="全部" value=""/>
                <el-option v-for="item in configs.managementTypes" :key="item.id" :label="item.name" :value="item.id"/>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item prop="sLeaseTypeId" label="租赁类型">
              <el-select clearable placeholder="租赁类型" v-model="searchParams.sLeaseTypeId" style="width: 170px;">
                <el-option label="全部" value=""/>
                <el-option v-for="item in configs.leaseTypes" :key="item.id" :label="item.name" :value="item.id"/>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-collapse-transition>
          <div v-show="advanced">
            <el-row>
              <el-col :span="6">
                <el-form-item prop="sRentAreaMin" label="出租面积最小值">
                  <el-input clearable placeholder="出租面积最小值" size="mini" type="number" v-model.number="searchParams.sRentAreaMin"
                            style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sRentAreaMax" label="出租面积最大值">
                  <el-input clearable placeholder="出租面积最大值" size="mini" type="number" v-model.number="searchParams.sRentAreaMax"
                            style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sRegAreaMin" label="注册面积最小值">
                  <el-input clearable placeholder="注册面积最小值" size="mini" type="number" v-model.number="searchParams.sRegAreaMin"
                            style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sRegAreaMax" label="注册面积最大值">
                  <el-input clearable placeholder="注册面积最大值" size="mini" type="number" v-model.number="searchParams.sRegAreaMax"
                            style="width: 170px;"/>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="6">
                <el-form-item prop="sContractDateMin" label="签约日期开始">
                  <el-date-picker v-model="searchParams.sContractDateMin" type="date" placeholder="签约日期开始"
                                  value-format="yyyy-MM-dd" style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sContractDateMax" label="签约日期结束">
                  <el-date-picker v-model="searchParams.sContractDateMax" type="date" placeholder="签约日期结束"
                                  value-format="yyyy-MM-dd" style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sAgreementStartAt" label="合同起租日">
                  <el-date-picker v-model="searchParams.sAgreementStartAt" type="date" placeholder="合同起租日"
                                  value-format="yyyy-MM-dd" style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sAgreementEndAt" label="合同到期日">
                  <el-date-picker v-model="searchParams.sAgreementEndAt" type="date" placeholder="合同到期日"
                                  value-format="yyyy-MM-dd" style="width: 170px;"/>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="6">
                <el-form-item prop="sContractStartAt" label="协议起租日">
                  <el-date-picker v-model="searchParams.sContractStartAt" type="date" placeholder="协议起租日"
                                  value-format="yyyy-MM-dd" style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sContractEndAt" label="协议到期日">
                  <el-date-picker v-model="searchParams.sContractEndAt" type="date" placeholder="协议到期日"
                                  value-format="yyyy-MM-dd" style="width: 170px;"/>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sTenantSourceId" label="成交类型">
                  <el-select clearable placeholder="成交类型" v-model="searchParams.sTenantSourceId" style="width: 170px;">
                    <el-option label="全部" value=""/>
                    <el-option v-for="item in configs.sourceTypes" :key="item.id" :label="item.name" :value="item.id"/>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sTenantSourceId" label="协议状态">
                  <el-select filterable clearable placeholder="协议状态" v-model="searchParams.SAgreementStateId"
                             style="width: 170px;">
                    <el-option label="全部" value=""/>
                    <el-option v-for="item in configs.contractStates" :key="item.id" :label="item.name"
                               :value="item.id"/>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="6">
                <el-form-item prop="sManagementIncluded" label="是否含管理费">
                  <el-select clearable placeholder="是否含管理费" v-model="searchParams.sManagementIncluded"
                             style="width: 170px;">
                    <el-option label="全部" value=""/>
                    <el-option label="是" value="1"/>
                    <el-option label="否" value="0"/>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item prop="sTaxIncluded" label="是否含税">
                  <el-select clearable placeholder="是否含税" v-model="searchParams.sTaxIncluded" style="width: 170px;">
                    <el-option label="全部" value=""/>
                    <el-option label="是" value="1"/>
                    <el-option label="否" value="0"/>
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
      <el-table :data="dataList" border class="custom-table" size="mini"
                v-loading="loading">
        <!--:summary-method="getSum" show-summary>-->
        <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
        <el-table-column align="center" label="序号" type="index" width="50px;"/>
        <el-table-column align="center" label="房号" prop="roomNum" width="100px;"/>
        <el-table-column align="center" label="出租面积(m²)" prop="rentArea" width="150px;"/>
        <el-table-column align="center" label="注册面积(m²)" prop="regArea" width="150px;"/>
        <el-table-column align="center" label="承租方" prop="tenantName" width="100px;"/>
        <el-table-column align="center" label="公司法人" prop="corpRepresentative" width="100px;"/>
        <el-table-column align="center" label="业务类型" prop="managementType" width="100px;"/>
        <el-table-column align="center" label="签约人" prop="contractor" width="100px;"/>
        <el-table-column align="center" label="签约人电话" prop="contractorMobile" width="150px;"/>
        <el-table-column align="center" label="签约日期" prop="contractDate" width="100px;"/>
        <el-table-column align="center" label="协议编号" prop="ticketNumber" width="150px;"/>
        <el-table-column align="center" label="合同号" prop="agreementNumber" width="170px;"/>
        <el-table-column align="center" label="合同起租日" prop="agreementStartAt" width="100px;"/>
        <el-table-column align="center" label="合同到期日" prop="agreementEndAt" width="100px;"/>
        <el-table-column align="center" label="协议起租日" prop="contractStartAt" width="100px;"/>
        <el-table-column align="center" label="协议到期日" prop="contractEndAt" width="100px;"/>
        <el-table-column align="center" label="租赁类型" prop="leaseType" width="150px;"/>
        <el-table-column label="押金(元)" align="right" prop="deposits" width="150px;"/>
        <el-table-column label="租金(元/月)" align="right" prop="monthlyRentFee" width="150px;"/>
        <el-table-column align="center" label="是否含管理费" prop="managementIncluded" width="100px;"/>
        <el-table-column align="center" label="是否含税" prop="taxIncluded" width="100px;"/>
        <el-table-column align="center" label="成交类型" prop="tenantSource" width="100px;"/>
        <el-table-column align="center" label="地产公司" prop="estCompany" width="100px;"/>
        <el-table-column align="center" label="地产销售代表" prop="estSalesman" width="100px;"/>
        <el-table-column align="center" label="递增明细" width="1000px;">
          <template slot-scope="scope">
            <p v-for='incr in scope.row.incrStr'>{{ incr }}</p>
          </template>
        </el-table-column>
        <el-table-column label="备注" prop="remark"/>
        <el-table-column label="协议状态" prop="agreementState"/>
      </el-table>
      <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                     @current-change="changePage"
                     @size-change="changeSize" background
                     layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
      </el-pagination>
    </el-row>


  </div>


</template>

<script>
import api from 'api'
import NP from 'number-precision';

export default {
  name: '',
  data() {
    return {
      advanced: false,//查询条件展开标识
      page: 1,
      pageSize: 10,
      dataList: [],
      totalSize: 10,
      loading: false,
      searchParams: {
        sRoomNum: "",//报表房号
        sRentAreaMin: "",//报表出租面积最小值
        sRentAreaMax: "",//报表出租面积最大值
        sRegAreaMin: "",//表注册面积最小值
        sRegAreaMax: "",//报表注册面积最大值
        sTenantName: "",//报表承租公司
        sManagementTypeId: "",//报表业务类型
        sContractDateMin: "",//报表签约日期开始
        sContractDateMax: "",//报表签约日期结束
        sAgreementStartAt: "",//报表合同起租日
        sAgreementEndAt: "",//报表合同到期日
        sContractStartAt: "",//报表协议起租日
        sContractEndAt: "",//报表协议到期日
        sLeaseTypeId: "",//报表租赁类型
        sManagementIncluded: "",//报表是否含管理费
        sTaxIncluded: "",//报表是否含税
        sTenantSourceId: "",//报表成交类型
        SAgreementStateId: "",//报表协议状态
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
      title: '报表管理/商务点出租统计表',
      subTitle: '报表管理/商务点出租统计表',
      action: 'listHtTenantStatement'
    });
    if (this.$route.query.tenantName) {
      this.searchParams.tenantName = this.$route.query.tenantName;
    }
    if (this.$route.query.tenantId) {
      this.searchParams.tenantId = this.$route.query.tenantId;
    }
    this.loadData();
  },
  methods: {
    /*hasPerms: function (perms) {
      // 根据权限标识和外部指示状态进行权限判断
      return hasPermission(perms)
    },*/

    /**
     * 加载数据
     * @param page
     * @returns {Promise<void>}
     */
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
      const result = await api.htTenantAgreement.listStatement(params);
      this.loading = false;
      if (result.code === 200) {
        this.$nextTick(() => {
          this.dataList = result.data.records;
          this.dataList.forEach(item => {
            item.roomNum = item.roomNum.replace(/\[|]| |"/g, '')
            item.managementIncluded = item.managementIncluded !== (1 || '1') ? "否" : "是";
            item.taxIncluded = item.taxIncluded !== (1 || '1') ? "否" : "是";
            item.incrStr = this.showIncrList(item.incrDetailsList)
          })
          this.totalSize = result.data.total * 1;
        })
      } else {
        this.$message.error(result.msg);
      }
    },

    /**
     * 重置搜索
     * @param searchParams
     */
    resetForm(searchParams) {
      this.$refs[searchParams].resetFields();
      this.loadData(1);
    },

    /**
     * 翻页
     */
    changePage(pageNo) {
      this.page = pageNo;
      this.loadData();
    },

    /**
     * 修改数量
     */
    changeSize(pageSize) {
      this.pageSize = pageSize;
      this.page = 1;
      this.loadData();
    },

    /**
     * 求和
     */
    getSum(param) {
      const {columns, data} = param;
      const sums = [];
      columns.forEach((column, index) => {
        //2,3,17,18
        sums[index] = index;
        if (index === 0) {
          sums[index] = '合计';
          return;
        }
        const values = data.map(item => Number(item[column.property]));
        if (index === 2 || index === 3 || index === 17 || index === 18) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);
            if (!isNaN(value)) {
              return NP.plus(prev, curr).toFixed(2);
            } else {
              return prev;
            }
          }, 0);
          /*if (index === 2 || index === 3) {
            sums[index] += ' m²';
          }
          if (index === 17) {
            sums[index] += ' 元/月';
          }
          if (index === 18) {
            sums[index] += ' 元';
          }*/
        } else {
          sums[index] = '';
        }
      });
      return sums;
    },

    /**
     * 递增周期
     * @param incrDetailsList
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

  }
}
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {

  .right-handle-group {
    float: right;
  }

  .el-table {
    overflow: auto;
  }

  .el-table .el-table__body-wrapper,
  .el-table .el-table__header-wrapper,
  .el-table .el-table__footer-wrapper {
    overflow: visible;
  }

  .el-table::after {
    position: relative !important;
  }

}
</style>
