<template>
  <div class="main-content">
    <el-form class="data-form" :model="dataForm" ref="dataForm">
      <el-form-item>
        <el-input v-model="dataForm.agreementNumber" readonly>
          <template slot="prepend">租赁协议编号</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.zoneName" readonly>
          <template slot="prepend">区域</template>
        </el-input>
        <el-input v-model="dataForm.buildingName" readonly>
          <template slot="prepend">楼宇</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.buildingProvince + dataForm.buildingCity" readonly>
          <template slot="prepend">楼宇地址</template>
        </el-input>
        <el-input v-model="dataForm.buildingAddress" readonly>
          <template slot="prepend">详细地址</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.roomNum" readonly>
          <template slot="prepend">房号</template>
        </el-input>
      </el-form-item>
      <el-row>
        <el-form-item label="仪表底数" label-width="100px">
          <el-table :data="dataForm.meterList" style="width: 100%;margin-right: 10%" stripe border>
            <el-table-column label="序号" width="50px" type="index"/>
            <el-table-column label="房间号" width="150px">
              <template slot-scope="scope">{{ scope.row.roomNum }}</template>
            </el-table-column>
            <el-table-column label="仪表编号" width="150px">
              <template slot-scope="scope">{{ scope.row.meterNumber }}</template>
            </el-table-column>
            <el-table-column label="仪表名称" width="150px">
              <template slot-scope="scope">{{ scope.row.meterName }}</template>
            </el-table-column>
            <el-table-column label="仪表类型" width="150px">
              <template slot-scope="scope">{{ scope.row.meterType }}</template>
            </el-table-column>
            <el-table-column label="仪表底数" width="180px">
              <template slot-scope="scope">{{ scope.row.meteBase }}</template>
            </el-table-column>
          </el-table>
        </el-form-item>
      </el-row>
      <el-form-item>
        <el-input v-model="dataForm.partyA" readonly>
          <template slot="prepend">出租方(甲方)</template>
        </el-input>
        <el-input v-model="dataForm.tenantName" readonly>
          <template slot="prepend">承租方(乙方)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aProvince + dataForm.aCity" readonly>
          <template slot="prepend">甲方地址</template>
        </el-input>
        <el-input v-model="dataForm.tenantProvince + dataForm.tenantCity" readonly>
          <template slot="prepend">乙方地址</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aAddress" readonly>
          <template slot="prepend">甲方详细地址</template>
        </el-input>
        <el-input v-model="dataForm.tenantAddress" readonly>
          <template slot="prepend">乙方详细地址</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aLinkman" readonly>
          <template slot="prepend">甲方联系人</template>
        </el-input>
        <el-input v-model="dataForm.contractor" readonly>
          <template slot="prepend">乙方联系人</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aContact" readonly>
          <template slot="prepend">甲方联系方式</template>
        </el-input>
        <el-input v-model="dataForm.contractorMobile" readonly>
          <template slot="prepend">乙方联系方式</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aOrgCode" readonly>
          <template slot="prepend">甲方组织机构代码证</template>
        </el-input>
        <el-input v-model="dataForm.tenantOrgCode" readonly>
          <template slot="prepend">乙方身份证/组织机构代码证</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aOrgCode" readonly v-if="dataForm.tenantTypeId === 1">
          <template slot="prepend">乙方公司法人</template>
        </el-input>
        <el-input v-model="dataForm.managementType" readonly>
          <template slot="prepend">乙方经营类型</template>
        </el-input>
      </el-form-item>
      <el-form-item label="甲方收款账户">
        <el-table :data="dataForm.accountList" style="width: 100%" stripe border>
          <el-table-column label="收款账户" width="330px">
            <template slot-scope="scope">
              <el-input v-model="scope.row.accountName" size="mini" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="开户行" width="200px">
            <template slot-scope="scope">
              <el-input v-model="scope.row.accountBank" size="mini" style="width: 175px" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="账号" width="200px">
            <template slot-scope="scope">
              <el-input v-model="scope.row.accountNumber" size="mini" style="width: 175px" readonly/>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.contractDate" readonly>
          <template slot="prepend">签约日期</template>
        </el-input>
        <el-input v-model="'每月'+dataForm.leasePaymentDate+'日'" readonly>
          <template slot="prepend">约定交租日期</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.agreementStartAt + ' 至 ' +dataForm.agreementEndAt" readonly>
          <template slot="prepend">协议租期</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.contractStartToEnd" readonly>
          <template slot="prepend">备案租期</template>
        </el-input>
        <el-input v-model="dataForm.ticketNumber" readonly>
          <template slot="prepend">租赁凭证编号</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.freeStartToEnd" readonly>
          <template slot="prepend">免租期</template>
        </el-input>
        <el-input v-model="dataForm.incrementalState ? '有' : '无'" readonly>
          <template slot="prepend">有无递增周期</template>
        </el-input>
      </el-form-item>
      <el-form-item label="递增周期" label-width="300" v-if="dataForm.incrementalState">
        <el-table :data="dataForm.incrDetailsList" style="width: 100%" stripe border>
          <el-table-column label="递增区间" width="330px">
            <template slot-scope="scope">
              <el-input v-model="scope.row.incrPeriodStartAt + ' 至 ' +scope.row.incrPeriodEndAt" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="递增比例(%)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.incrRatio" type="number" size="mini" style="width: 70px;" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="递增单价(元/m²/月)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.incrUnitPrice" type="number" size="mini" style="width: 70px;" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="递增金额(元)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.incrFee" type="number" size="mini" style="width: 70px;" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="递增后单价(元/m²/月)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.afterIncrUnitPrice" type="number" size="mini"
                        style="width: 70px;" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="递增后金额(元)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.afterIncrFee" type="number" size="mini" style="width: 70px;" readonly/>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.rentArea" readonly>
          <template slot="prepend">出租面积(m²)</template>
        </el-input>
        <el-input v-model="dataForm.regArea" readonly>
          <template slot="prepend">备案面积(m²)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.leaseType" readonly>
          <template slot="prepend">租赁类型</template>
        </el-input>
        <el-input v-model="dataForm.agreementType" readonly>
          <template slot="prepend">协议类型</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.elePricePerDegree" readonly>
          <template slot="prepend">电费标准(元/度)</template>
        </el-input>
        <el-input v-model="dataForm.waterPricePerTon" readonly>
          <template slot="prepend">水费标准(元/吨)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.airConditionFeesMethod" readonly>
          <template slot="prepend">空调费计算方式</template>
        </el-input>
        <el-input v-model="dataForm.airConditionFeesUnitPrice" readonly v-if="dataForm.airConditionFeesMethodId === 2 || dataForm.airConditionFeesMethodId === 3">
          <template slot="prepend">计费单价</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.monthlyRentFee" readonly>
          <template slot="prepend">月租金(元/月)</template>
        </el-input>
        <el-input v-model="dataForm.rentCollectionMethod">
          <template slot="prepend">租金收取方式</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.depositsType" readonly>
          <template slot="prepend">房屋押金类型</template>
        </el-input>
        <el-input v-model="dataForm.deposits" readonly>
          <template slot="prepend">房屋押金</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.otherDepositListStr" readonly style="width: 615px">
          <template slot="prepend">其他押金(元)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.decorationStatus?'是':'否'" readonly>
          <template slot="prepend">是否带装修</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.managementIncluded?'是':'否'" readonly>
          <template slot="prepend">租金是否包含管理费</template>
        </el-input>
        <el-input v-model="dataForm.managementFees" readonly v-if="!dataForm.managementIncluded">
          <template slot="prepend">管理费</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.taxIncluded?'是':'否'" readonly>
          <template slot="prepend">租金是否包含税</template>
        </el-input>
        <el-input v-model="dataForm.taxesFees" readonly v-if="!dataForm.taxIncluded">
          <template slot="prepend">税费</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.tenantSource" readonly>
          <template slot="prepend">成交信息来源</template>
        </el-input>
        <el-input v-model="dataForm.estCompany" readonly v-if="dataForm.tenantSourceId===1">
          <template slot="prepend">地产公司</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.estSalesman" readonly v-if="dataForm.tenantSourceId===1">
          <template slot="prepend">地产销售</template>
        </el-input>
        <el-input v-model="dataForm.estSlaesmanMobile" readonly v-if="dataForm.tenantSourceId===1">
          <template slot="prepend">地产销售手机号</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.remark" readonly>
          <template slot="prepend">备注</template>
        </el-input>
      </el-form-item>
      <el-table :data="filesList" border stripe style="width: 100%">
        <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无文件</span></div>
        <el-table-column prop="fileOldName" label="已上传的文件"></el-table-column>
        <el-table-column prop="createTime" label="上传时间"></el-table-column>
        <el-table-column prop="createUser" label="上传人"></el-table-column>
        <el-table-column label="操作" width="130px">
          <template slot-scope="scope">
            <el-button size="small" type="text" @click="preview(scope.row)">查看</el-button>
            <el-button size="small" type="text" @click="downloadfiles(scope.row)">下载
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <br>
      <div id="uploadFiles">
        附件(支持JPG/PNG/PDF)<label style="margin-left: 30px;color: red"> 请先选取文件，确定后再上传文件</label>
        <el-upload
          class="upload-demo"
          :action="uploadUrl"
          ref="upload"
          :before-upload="beforeUpload"
          :on-success="handleAvatarSuccess"
          :on-preview="preview"
          :auto-upload="false"
        >
          <el-button size="small" slot="trigger" type="primary" style="margin:10px">选取文件</el-button>
          <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">确定上传</el-button>
        </el-upload>
      </div>
      <br>
      <el-form-item label="">
        <el-button type="default" @click="back" size="small">关闭</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import api from 'api';
import {copy, getLocalItem} from "utils/utils";
import {API_SERVER, IMG_SERVER} from "../../../config";

export default {
  name: '',
  data() {
    return {
      filesList: [],
      files: [],
      fileIds:[],//文件id集合
      imgServer: IMG_SERVER,
      uploadUrl: `${API_SERVER}${api.upload.uploadImg()}?x-admin-token=${getLocalItem(
        "accessToken"
      )}&fileCode=3`,
      FileList: {
        id: "",
        fileName: "",
        businessCode: "tenant_agreement_attachment",
        businessId: "",
        fileOldName: "",
        fileType: "",
        fileUrl: "",
        createTime: "",
        createUser: "",
      },
      sending: false,
      dataForm: {},
    }
  },
  computed: {
    configs() {
      return this.$store.getters["sys/configs"];
    }
  },
  async mounted() {
    this.$store.dispatch('breadcrumb/addPath', {
      title: '合同管理/租户协议详情',
      subTitle: '合同管理/租户协议详情',
      action: 'HtTenantAgreementInfo'
    });
    this.loadData();
  },
  methods: {

    /**
     * 加载数据
     */
    async loadData() {
      const result = await api.htTenantAgreement.getById(this.$route.params.id);
      if (result.code == 200) {
        this.dataForm = result.data;
        this.dataForm.roomNum = this.dataForm.roomNum.replace(/\[|]| |"/g, '')
        this.dataForm.freeStartToEnd = this.dataForm.freePeriod === 1 ? this.dataForm.freeStartAt + " 至 " + this.dataForm.freeEndAt : "无";
        this.dataForm.otherDepositListStr = this.showOtherDeposit(this.dataForm.otherDepositList)
        this.dataForm.contractStartToEnd = (this.dataForm.contractStartAt != null && this.dataForm.contractEndAt != null) ? (this.dataForm.contractStartAt + " 至 " + this.dataForm.contractEndAt) : "";
      } else {
        this.$message.error(result.msg);
      }
      const res = await api.upload.getFile(this.$route.params.id, this.FileList.businessCode);
      this.files.push(res.data)
      if (res.code === 200) {
        this.files.forEach((item) => {
          for (let i = 0; i < item.length; i++) {
            this.filesList.push(item[i]);
            copy(this.FileList, item[i]);
          }
        })
      }
    },

    /**
     * 返回
     */
    back() {
      this.$router.go(-1);
    },

    preview(row) {
      api.upload.previewFile(row.fileName).then(result => {
        if (result.code === 200) {
          result.data.forEach(item => {
            let url = `${IMG_SERVER}${item.fileUrl}`;
            window.open(url, '_blank')
          })
        }
      })
    },
    //确定上传
    submitUpload(file) {
      this.$refs.upload.submit();
    },
    //上传文件前的钩子
    beforeUpload(file) {
      const isJPG = file.type === "image/jpeg";
      const isPNG = file.type === "image/png";
      const isPDF = file.type === "application/pdf";
      const isLt2M = file.size / 1024 / 1024 < 10;

      if (!isJPG && !isPNG && !isPDF) {
        this.$message.error("上传扫描件只能是 JPG/PNG/PDF 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传扫描件大小不能超过 10MB!");
      }
      return (isJPG || isPNG || isPDF) && isLt2M;
    },
    //上传文件成功后的钩子
    handleAvatarSuccess(res, file) {
      if (res.code === 200) {
        this.$message.success('上传成功!');
        this.fileIds.push(res.data.fileId);
        api.upload.syncFile(this.fileIds,this.$route.params.id);
      }
    },
    /**
     * 下载文件
     * @param row
     */
    downloadfiles(row) {
      let link = `${API_SERVER}/api/rest/1.0/upload/download`;
      axios.get(link, {
        params: {
          fileName: row.fileName,
          isOnline: false
        }, responseType: 'blob'
        , headers: {"Content-Type": "application/x-msdownload"},
      })
        .then(res => {
          let url = window.URL.createObjectURL(res.data); //创建一个新的 URL 对象
          //以下代码一句话解释，在页面上生成一个a标签并指定href为上面的url,然后模拟点击，以实现自动下载
          let a = document.createElement("a");
          document.body.appendChild(a);
          a.href = url;
          a.download = `${row.fileOldName}`
          a.click();
          window.URL.revokeObjectURL(url);
        })
        .catch((err) => {
        });
    },

    /**
     * 其他押金
     * @param deposits
     * @returns {string}
     */
    showOtherDeposit(deposits) {
      if (!deposits) {
        return "无";
      }
      if (deposits.length > 0) {
        let strList = [];
        deposits.forEach(item => {
          let str = item.name + ":" + item.deposit + "元";
          strList.push(str);
        })
        return strList.join();
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

  textarea {
    width: 480px
  }
}
</style>
