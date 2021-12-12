<template>
  <div class="main-content">
    <el-row class="mb-10">
      <div class="l-title">预算金额</div>
      <el-form :rules="dataRules" class="data-form" label-position="left" ref="dataForm" size="small" :model="dataForm">
        <el-form-item>
          <span style="padding-right: 10px">装修工程预算金额:</span>
          <el-input label-width="20px" v-model="dataForm.decorationPreAmount" readonly>
            <template slot="append">元</template>
          </el-input>
          <span style="padding: 0 10px 0 50px">装修工程预结算金额:</span>
          <el-input @change="calc" label-width="20px" v-model="dataForm.decorationAmount" type="number" :min="0">
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <span style="padding-right: 10px">空调工程预算金额:</span>
          <el-input label-width="20px" v-model="dataForm.conditionerPreAmount" readonly>
            <template slot="append">元</template>
          </el-input>
          <span style="padding: 0 10px 0 50px">空调工程预结算金额:</span>
          <el-input @change="calc" label-width="20px" v-model="dataForm.conditionerAmount" type="number" :min="0">
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <span style="padding-right: 10px">弱电工程预算金额:</span>
          <el-input label-width="20px" v-model="dataForm.elvPreAmount" readonly>
            <template slot="append">元</template>
          </el-input>
          <span style="padding: 0 10px 0 50px">弱电工程预结算金额:</span>
          <el-input @change="calc" label-width="20px" v-model="dataForm.elvAmount" min="0">
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <span style="padding-right: 10px">消防工程预算金额:</span>
          <el-input label-width="20px" v-model="dataForm.firePreAmount" readonly>
            <template slot="append">元</template>
          </el-input>
          <span style="padding: 0 10px 0 50px">消防工程预结算金额:</span>
          <el-input @change="calc" label-width="20px" v-model="dataForm.fireAmount" min="0">
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <span style="padding-right: 10px">合计工程预算金额:</span>
          <el-input label-width="20px" v-model="dataForm.totalPreAmount" readonly>
            <template slot="append">元</template>
          </el-input>
          <span style="padding: 0 10px 0 50px">合计工程预结算金额:</span>
          <el-input label-width="20px" v-model="dataForm.totalAmount" readonly>
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item prop="settlementTime" label="预结算时间" style="margin-left: 51%;line-height: 32px;font-size: 14px">
          <el-date-picker clearable format="yyyy-MM-dd" type="date" v-model="dataForm.settlementTime"
                          value-format="yyyy-MM-dd"/>
        </el-form-item>
        <el-form-item>
          <el-button :disabled="sending" @click="back" size="small" type="default">取 消</el-button>
          <el-button :disabled="sending" :loading="sending" @click="save" v-if="hasPerms('tenant:YJSCommit')" class="btn-save" size="small" type="primary">
            {{ sending ? '处理中...' : '提 交' }}
          </el-button>
        </el-form-item>
      </el-form>
    </el-row>
    <div class="l-title">预结算记录</div>
    <el-table :data="htPreList" border class="custom-table" size="mini">
      <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
      <el-table-column label="序号" type="index"></el-table-column>
      <el-table-column label="装修工程预结算金额" prop="decorationAmount"/>
      <el-table-column label="空调工程预结算金额" prop="conditionerAmount"/>
      <el-table-column label="弱电工程预结算金额" prop="fireAmount"/>
      <el-table-column label="消防工程预结算金额" prop="elvAmount"/>
      <el-table-column label="合计预结算金额" prop="totalAmount"/>
      <el-table-column label="结算时间" prop="settlementTime"/>
      <el-table-column label="操作" prop="" width="250px;" fixed="right">
        <template slot-scope="scope">
          <el-button size="mini" @click="upInfo(scope.row)" type="text" v-if="hasPerms('tenant:YJSEdit')">编辑</el-button>
          <el-button size="mini" @click="del(scope.row)" type="text" v-if="hasPerms('tenant:YJSDel')">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
import api from 'api'
import {hasPermission} from "../../../permission";

export default {
  name: '',
  data() {
    return {
      sending: false,
      showAddDialog: false,
      showUpDialog: false,
      contractId: this.$route.params.id,
      addOrUp: true,
      htPreList: [],
      dataForm: {
        id: '',
        decorationPreAmount: 0,
        decorationAmount: 0,
        conditionerPreAmount: 0,
        conditionerAmount: 0,
        elvPreAmount: 0,
        elvAmount: 0,
        firePreAmount: 0,
        fireAmount: 0,
        totalPreAmount: 0,
        totalAmount: 0,
        settlementTime: '',
      },
      dataRules: {
        settlementTime: [
          {required: true, message: '预结算时间不能为空', trigger: 'blur'}
        ],
      }
    }
  },
  async mounted() {
    await this.$store.dispatch('breadcrumb/addPath', {
      title: '业主合同管理/工程预结算',
      subTitle: '业主合同管理/工程预结算',
      action: 'editHtPreSettlement'
    });
    await this.loadData()
  },
  methods: {
    hasPerms: function (perms) {
      // 根据权限标识和外部指示状态进行权限判断
      return hasPermission(perms)
    },
    async loadData() {
      const result = await api.htPreSettlement.listAll(this.contractId);
      if (result.code == 200) {
        const obj = result.data;
        this.dataForm.elvPreAmount = obj.elvPreAmount;
        this.dataForm.firePreAmount = obj.firePreAmount;
        this.dataForm.decorationPreAmount = obj.decorationPreAmount;
        this.dataForm.conditionerPreAmount = obj.conditionerPreAmount;
        this.dataForm.totalPreAmount = obj.conditionerPreAmount + obj.decorationPreAmount + obj.firePreAmount
          + obj.elvPreAmount;
        this.htPreList = obj.htPreList
        this.htPreList.forEach(ht => {
          ht.totalAmount = ht.decorationAmount + ht.conditionerAmount + ht.fireAmount + ht.elvAmount;
        })
      } else {
        this.$message.error(result.msg);
      }
    },
    calc() {
      var totalAmount = 0;
      totalAmount += Number(this.dataForm.decorationAmount);
      totalAmount += Number(this.dataForm.elvAmount);
      totalAmount += Number(this.dataForm.conditionerAmount);
      totalAmount += Number(this.dataForm.fireAmount);

      this.dataForm.totalAmount = totalAmount;
    },
    async save() {
      this.$refs.dataForm.validate(async (valid) => {
        if (valid) {
          if (this.dataForm.fireAmount === 0 && this.dataForm.decorationAmount === 0 && this.dataForm.conditionerAmount === 0 && this.dataForm.elvAmount === 0) {
            this.$message.error("请至少填写一项预结算金额");
            return;
          }
          const tipMsg = "本次工程预结算金额：装修工程" + this.dataForm.decorationAmount + "元,空调工程" +
            this.dataForm.conditionerAmount + "元,弱电工程" + this.dataForm.elvAmount +
            "元,消防工程" + this.dataForm.totalAmount + "元,合计" + this.dataForm.totalAmount + "元,结算时间"
            + this.dataForm.settlementTime + ",是否确认保存?";
          this.$confirm(tipMsg, '预结算信息确认', {
            distinguishCancelAndClose: true,
            confirmButtonText: '确认',
            cancelButtonText: '取消'
          })
            .then(async () => {
              this.sending = true;
              let params = Object.assign({}, this.dataForm);
              params.contractId = this.contractId;
              const result = await api.htPreSettlement[this.addOrUp ? 'add' : 'updateById'](params);
              const tip = this.addOrUp ? '新增成功' : '更新成功';
              this.sending = false;
              this.addOrUp = true;
              if (result.code == 200) {
                this.$message.success(tip);
                this.showAddDialog = false;
                this.dataForm.elvAmount = 0;
                this.dataForm.fireAmount = 0;
                this.dataForm.decorationAmount = 0;
                this.dataForm.conditionerAmount = 0;
                this.dataForm.settlementTime = "";
                await this.loadData();
              } else {
                this.$message.error(result.msg);
              }
            })
            .catch(action => {
            });
        }
      })
    },
    async upInfo(row) {
      const res = await api.htPreSettlement.getById(row.id);
      if (res.code === 200) {
        const obj = res.data;
        this.dataForm.id = obj.id;
        this.dataForm.elvAmount = obj.elvAmount;
        this.dataForm.fireAmount = obj.fireAmount;
        this.dataForm.decorationAmount = obj.decorationAmount;
        this.dataForm.conditionerAmount = obj.conditionerAmount;
        this.dataForm.settlementTime = obj.settlementTime;
        this.addOrUp = false;
      } else {
        this.$message.error(res.msg);
      }
    },
    async del(row) {
      this.$confirm("确定要删除预结算记录?", "删除预结算记录", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htPreSettlement.deleteById(row.id);
          if (res.code == 200) {
            this.$message.success("删除成功");
            await this.loadData();
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
    },
    back() {
      this.$router.go(-1);
    },
  },
  watch: {
    "dataForm.decorationAmount"(id) {
      if (id < 0) {
        this.dataForm.decorationAmount = 0;
        return;
      }
      this.calc();
    },
    "dataForm.conditionerAmount"(id) {
      if (id < 0) {
        this.dataForm.conditionerAmount = 0;
        return;
      }
      this.calc();
    },
    "dataForm.elvAmount"(id) {
      if (id < 0) {
        this.dataForm.elvAmount = 0;
        return;
      }
      this.calc();
    },
    "dataForm.fireAmount"(id) {
      if (id < 0) {
        this.dataForm.fireAmount = 0;
        return;
      }
      this.calc();
    },
  }
}
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {
  .item {
    background: inherit;
    background-color: rgba(242, 242, 242, 1);
    box-sizing: border-box;
    border-width: 1px;
    border-style: solid;
    border-color: rgba(242, 242, 242, 1);
    border-radius: 0px;
    width: 880px;
    height: 38px;
  }

  #total > .el-input {
    width: 277px;
  }
}
</style>
