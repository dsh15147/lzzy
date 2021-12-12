<template>
  <div class="main-content">
    <el-form class="data-form" :model="dataForm" ref="dataForm">
      <el-form-item>
        <el-input v-model="dataForm.contractNum" readonly>
          <template slot="prepend">合同编号</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.partyA" readonly>
          <template slot="prepend">合同甲方</template>
        </el-input>
        <el-input v-model="dataForm.aLinkman" readonly>
          <template slot="prepend">甲方联系人</template>
        </el-input>
        <el-input v-model="dataForm.aContact" readonly>
          <template slot="prepend">甲方联系方式</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aProvince + dataForm.aCity + dataForm.aAddress" style="width: 50%;" readonly>
          <template slot="prepend">甲方地址</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.aOrgCode" style="width: 66%;" readonly>
          <template slot="prepend">甲方身份证/组织机构代码证</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.partyB" readonly>
          <template slot="prepend">合同乙方</template>
        </el-input>
        <el-input v-model="dataForm.bLinkman" readonly>
          <template slot="prepend">乙方联系人</template>
        </el-input>
        <el-input v-model="dataForm.bContact" readonly>
          <template slot="prepend">乙方联系方式</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.bProvince + dataForm.bCity + dataForm.bAddress" style="width: 50%;" readonly>
          <template slot="prepend">乙方地址</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.bOrgCode" style="width: 66%;" readonly>
          <template slot="prepend">乙方组织机构代码证</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.zoneName" readonly>
          <template slot="prepend">所属区域</template>
        </el-input>
        <el-input v-model="dataForm.buildingName" readonly>
          <template slot="prepend">楼宇</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.buildingProvince + dataForm.buildingCity + dataForm.buildingAddress"
                  style="width: 50%" readonly>
          <template slot="prepend">地址</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.filed?'是':'否'" readonly>
          <template slot="prepend">是否备案</template>
        </el-input>
        <el-input v-model="dataForm.rentFiledNum" readonly>
          <template slot="prepend">租赁备案号</template>
        </el-input>
        <el-input v-model="dataForm.contractDate" readonly>
          <template slot="prepend">签约日期</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="'每月' + dataForm.leasePaymentDate + '日'" readonly>
          <template slot="prepend">约定交租日期</template>
        </el-input>
        <el-input v-model="dataForm.rentArea" readonly>
          <template slot="prepend">承租面积(m²)</template>
        </el-input>
        <el-input v-model="dataForm.rentPricePerSquare" readonly>
          <template slot="prepend">承租单价(元/m²)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.monthlyFee" readonly>
          <template slot="prepend">月租金(元/月)</template>
        </el-input>
        <el-input v-model="dataForm.deposits" readonly>
          <template slot="prepend">房屋押金(元)</template>
        </el-input>
        <el-input v-model="dataForm.tenancyTerm" readonly>
          <template slot="prepend">租期(年)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.rentStartAt + '~' + dataForm.rentEndAt" readonly>
          <template slot="prepend">起租日</template>
        </el-input>
        <el-input v-model="dataForm.freeStartToEnd" readonly>
          <template slot="prepend">免租期</template>
        </el-input>
        <el-input v-model="dataForm.manPricePerSquare" readonly>
          <template slot="prepend">管理费标准(元/m²)</template>
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
        <el-input v-model="dataForm.electricityMeterReading" readonly>
          <template slot="prepend">电表底数(度)</template>
        </el-input>
        <el-input v-model="dataForm.waterMeterReading" readonly>
          <template slot="prepend">水表底数(吨)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.otherDepositListStr" readonly style="width: 615px">
          <template slot="prepend">其他押金(元)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.ohtersFeeListStr" style="width: 615px">
          <template slot="prepend">其他收费(元)</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.managementIncluded?'是':'否'" readonly>
          <template slot="prepend">是否含管理费</template>
        </el-input>
        <el-input v-model="dataForm.taxIncluded?'是':'否'" readonly>
          <template slot="prepend">是否含税</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.incrementalState?'有':'无'" readonly>
          <template slot="prepend">有无递增周期</template>
        </el-input>
      </el-form-item>
      <el-form-item label="递增周期" v-if="dataForm.incrementalState">
        <el-table :data="dataForm.incrDetailsList" style="width: 100%" stripe border>
          <el-table-column label="递增区间" width="300px">
            <template slot-scope="scope">
              <el-input v-model="scope.row.incrPeriodStartAt + ' 至 ' + scope.row.incrPeriodEndAt"
                        style="width: 260px;" readonly/>
            </template>
          </el-table-column>
          <el-table-column label="递增比例(%)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.incrRatio" type="number" size="mini" style="width: 70px;"/>
            </template>
          </el-table-column>
          <el-table-column label="递增单价(元/m²/月)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.incrUnitPrice" type="number" size="mini" style="width: 70px;"/>
            </template>
          </el-table-column>
          <el-table-column label="递增金额(元)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.incrFee" type="number" size="mini" style="width: 70px;"/>
            </template>
          </el-table-column>
          <el-table-column label="递增后单价(元/m²/月)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.afterIncrUnitPrice" type="number" size="mini"
                        style="width: 70px;"/>
            </template>
          </el-table-column>
          <el-table-column label="递增后金额(元)" width="140px">
            <template slot-scope="scope">
              <el-input clearable v-model="scope.row.afterIncrFee" type="number" size="mini" style="width: 70px;"/>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item label="特殊条款" prop="specialTerms">
        <el-input type="textarea" v-model="dataForm.specialTerms" readonly/>
      </el-form-item>
      <el-table :data="arr" border stripe style="width: 100%">
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
import {IMG_SERVER} from "../../../config";
import api from "api";
import {copy, getLocalItem} from "utils/utils";
import {API_SERVER} from "../../../config";

export default {
  name: "",
  data() {
    return {
      arr: [],
      files: [],
      filename: "",
      fileIds: [],//文件id集合
      FileList: {
        id: "",
        fileName: "",
        businessCode: "HouseProperty_FireApproval",
        businessId: "",
        fileOldName: "",
        fileType: "",
        fileUrl: "",
        createTime: "",
        createUser: "",
      },
      uploadUrl: `${API_SERVER}${api.upload.uploadImg()}?x-admin-token=${getLocalItem(
        "accessToken"
      )}&fileCode=1`,
      defaultImage: 'this.src="static/clear.png"',
      sending: false,
      imgServer: IMG_SERVER,
      dataForm: {}
    };
  },
  async mounted() {
    this.$store.dispatch("breadcrumb/addPath", {
      title: "合同管理/业主合同详情",
      subTitle: "合同管理/业主合同详情",
      action: "HtOwnerContractInfo"
    });
    this.loadData();
  },
  methods: {
    async loadData() {
      const res = await api.upload.getFile(this.$route.params.id, this.FileList.businessCode);
      this.files.push(res.data)
      if (res.code === 200) {
        this.files.forEach((item) => {
          for (let i = 0; i < item.length; i++) {
            this.arr.push(item[i]);
            copy(this.FileList, item[i]);
          }
        })
        for (let i = 0; i < this.arr.length; i++) {
          this.filename = this.arr[i].fileName;
        }
      }
      const result = await api.htOwnerContract.getById(this.$route.params.id);
      if (result.code == 200) {
        this.dataForm = result.data;
        this.dataForm.freeStartToEnd = this.dataForm.freePeriod === 1 ? this.dataForm.freeStartAt + " 至 " + this.dataForm.freeEndAt : "无";
        this.dataForm.ohtersFeeListStr = this.showOtherFee(this.dataForm.otherFeeList)
        this.dataForm.otherDepositListStr = this.showOtherDeposit(this.dataForm.otherDepositList)
      } else {
        this.$message.error(result.msg);
      }
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
    //下载
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
          //在页面上生成一个a标签并指定href为上面的url,然后模拟点击，以实现自动下载
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
    //查看文件
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
    showOtherFee(fees) {
      if (!fees) {
        return "无";
      }
      let str = ""
      if (fees.length > 0) {
        let strList = [];
        fees.forEach(item => {
          let str =  item.name + ":" + item.fee + "元"
          strList.push(str);
        })
        return strList.join();
      } else {
        return  "无";
      }
    },
    showOtherDeposit(deposits) {
      if (!deposits) {
        return "无";
      }
      if (deposits.length > 0) {
        let strList = [];
        deposits.forEach(item => {
          let str = item.name + ":" + item.deposit + "元"
          strList.push(str)
        })
        return strList.join();
      } else {
        return "无"
      }
    },
    back() {
      this.$router.go(-1);
    }
  }
};
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {
  .el-input--small .el-input__inner {
    height: 38px;
    line-height: 32px;
  }

  .el-input-group__append,
  .el-input-group__prepend {
  }

  textarea {
    width: 480px;
  }

  .avatar {
    width: 320px;
    height: 240px;
    display: block;
  }
}
</style>
