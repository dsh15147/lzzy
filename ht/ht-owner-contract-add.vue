<template>
  <div class="main-content">
    <el-form :model="dataForm" :rules="dataRules" class="data-form" label-position="left"
             label-width="200px" ref="dataForm" size="small">
      <el-row>
        <el-col :span="24">
          <el-form-item label="合同编号" prop="contractNum">
            <el-input disabled v-model="dataForm.contractNum"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="合同甲方(业主)" prop="ownerId">
            <el-select clearable filterable placeholder="请选择业主" v-model="dataForm.ownerId"
                       @change="selectOwner" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="item.id" :label="item.ownerName" :value="item.id" v-for="item in owners"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="合同乙方(经营公司)" prop="partyBId">
            <el-select clearable filterable v-model="dataForm.partyBId"
                       @change="selectCompany" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="index" :label="item.companyName" :value="item.id"
                         v-for="(item, index) in companyList"/>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方地址" prop="aProvinceAndCity">
            <el-cascader clearable :options="provinceAndCityList" v-model="dataForm.aProvinceAndCity"
                         placeholder="选择合同甲方(业主)后自动带出" disabled/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方地址" prop="bProvinceAndCity">
            <el-cascader clearable :options="provinceAndCityList" v-model="dataForm.bProvinceAndCity"
                         placeholder="选择合同乙方(经营公司)后自动带出" disabled/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方详细地址" prop="aAddress">
            <el-input clearable v-model="dataForm.aAddress" placeholder="选择合同甲方(业主)后自动带出" disabled/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方详细地址" prop="bAddress">
            <el-input clearable v-model="dataForm.bAddress" placeholder="选择合同乙方(经营公司)后自动带出" disabled/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方联系人" prop="aLinkman">
            <el-input clearable v-model="dataForm.aLinkman"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方联系人" prop="bLinkman">
            <el-input clearable v-model="dataForm.bLinkman"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方联系方式" prop="aContact">
            <el-input clearable v-model="dataForm.aContact"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方联系电话" prop="bContact">
            <el-input clearable v-model="dataForm.bContact"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方身份证/组织机构代码证" prop="aOrgCode">
            <el-input clearable v-model="dataForm.aOrgCode"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方组织机构代码证" prop="bOrgCode">
            <el-input clearable v-model="dataForm.bOrgCode"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="区域" prop="zoneId">
            <el-select clearable filterable placeholder="请选择区域" v-model="dataForm.zoneId"
                       @change="selectZone" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.zones"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="楼宇" prop="buildingId">
            <el-select clearable filterable placeholder="请选择楼宇" v-model="dataForm.buildingId"
                       @change="selectBuilding" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="index" :label="item.building" :value="item.buildingId"
                         v-for="(item, index) in buildings"/>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="楼宇地址" prop="buildingProvinceAndCity">
            <el-cascader clearable :options="provinceAndCityList" v-model="dataForm.buildingProvinceAndCity"
                         placeholder="选择楼宇后自动带出" disabled/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="详细地址" prop="buildingAddress">
            <el-input clearable v-model="dataForm.buildingAddress" placeholder="选择楼宇后自动带出" disabled/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="是否备案" prop="">
            <el-radio-group v-model="dataForm.filed" @change="setFiledReq">
              <el-radio :label="1">是</el-radio>
              <el-radio :label="0">否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="租赁备案号" prop="rentFiledNum" v-if="dataForm.filed === 1">
            <el-input clearable v-model="dataForm.rentFiledNum"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="签约日期" prop="contractDate">
            <el-date-picker clearable format="yyyy-MM-dd" type="date" v-model="dataForm.contractDate"
                            value-format="yyyy-MM-dd"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="约定交租日期" prop="leasePaymentDate">每月
            <el-input clearable type="number" v-model.number="dataForm.leasePaymentDate" style="width: 150px;"></el-input>
            日
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="承租面积(m²)" prop="rentArea">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.rentArea" @change="calrent"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="承租单价(元/m²/月)" prop="rentPricePerSquare">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.rentPricePerSquare" @change="calrent"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="月租金(元/月)" prop="monthlyFee">
            <el-input clearable type="number" v-model.number="dataForm.monthlyFee"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="租期(年)" prop="tenancyTerm">
            <el-input-number controls-position="right" v-model="dataForm.tenancyTerm"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="租期起始时间" prop="rentStartToEnd">
            <el-date-picker clearable v-model="dataForm.rentStartToEnd" type="daterange" range-separator="至"
                            start-placeholder="免租期开始日期" value-format="yyyy-MM-dd"
                            end-placeholder="免租期结束日期" size="mini"
                            :picker-options="dataForm.rentStartToEndpickerOptions"
                            :default-value="dataForm.rentStartToEndDefaultValue">
            </el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="有无免租期" prop="freePeriod">
            <el-radio-group v-model="dataForm.freePeriod" @change="setFreePeriodReq">
              <el-radio :label="1">有</el-radio>
              <el-radio :label="0">无</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="免租期起始时间" prop="freeStartToEnd" v-if="dataForm.freePeriod === 1">
            <el-date-picker clearable v-model="dataForm.freeStartToEnd" type="daterange" range-separator="至"
                            start-placeholder="免租期开始日期" value-format="yyyy-MM-dd"
                            end-placeholder="免租期结束日期" size="mini">
            </el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="有无递增周期" prop="incrementalState">
            <el-radio-group v-model="dataForm.incrementalState">
              <el-radio :label="1">有</el-radio>
              <el-radio :label="0">无</el-radio>
            </el-radio-group>
            <!--            <template>
                          <el-radio :label="1" v-model="dataForm.incrementalState">有</el-radio>
                          <el-radio :label="0" v-model="dataForm.incrementalState">无</el-radio>
                        </template>-->
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-form-item label="递增周期" label-width="300" v-if="dataForm.incrementalState === 1">
          <el-table :data="dataForm.incrDetailsList" style="width: 100%" stripe border>
            <el-table-column label="递增区间" width="500px">
              <template slot-scope="scope">
                <el-row>
                  <el-col :span="11">
                    <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrPeriodStartAt'"
                                  :rules="dataRules.incrPeriodStartAt" style="margin-bottom:0;">
                      <el-date-picker v-model="scope.row.incrPeriodStartAt" style="width: 200px;"
                                      type="date" placeholder="开始日期" format="yyyy-MM-dd"
                                      :disabled="scope.$index !== 0" value-format="yyyy-MM-dd"/>
                    </el-form-item>
                  </el-col>
                  <el-col :span="1">
                    <span style="width: 40px;">至</span>
                  </el-col>
                  <el-col :span="11">
                    <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrPeriodEndAt'"
                                  :rules="dataRules.incrPeriodEndAt" style="margin-bottom:0;margin-left: 20px;">
                      <el-date-picker v-if="scope.$index === 0" v-model="scope.row.incrPeriodEndAt"
                                      style="width: 200px;" type="date" placeholder="结束日期" format="yyyy-MM-dd"
                                      value-format="yyyy-MM-dd" @change="changeDate(scope.$index)"/>
                      <el-date-picker v-else v-model="scope.row.incrPeriodEndAt"
                                      style="width: 200px;" type="date" placeholder="结束日期" format="yyyy-MM-dd"
                                      value-format="yyyy-MM-dd" :picker-options="scope.row.pickerOptions" @change="changeDate(scope.$index)"/>
                    </el-form-item>
                  </el-col>
                </el-row>
              </template>
            </el-table-column>
            <el-table-column label="递增比例(%)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrRatio'"
                               :rules="dataRules.incrRatio" style="margin-bottom:0;">
                <el-input clearable @input="changeInfo(scope.$index)" v-model="scope.row.incrRatio"
                          type="number" size="mini" style="width: 100px;"/>
              </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="递增单价(元/m²/月)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrUnitPrice'"
                              :rules="dataRules.incrUnitPrice" style="margin-bottom:0;">
                  <el-input clearable v-model="scope.row.incrUnitPrice" type="number"
                            size="mini" style="width: 100px;"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="递增金额(元)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrFee'"
                              :rules="dataRules.incrFee" style="margin-bottom:0;">
                  <el-input clearable v-model="scope.row.incrFee" type="number"
                            size="mini" style="width: 100px;"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="递增后单价(元/m²/月)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.afterIncrUnitPrice'"
                              :rules="dataRules.afterIncrUnitPrice" style="margin-bottom:0;">
                  <el-input clearable v-model="scope.row.afterIncrUnitPrice" type="number" size="mini"
                            style="width: 100px;"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="递增后金额(元)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.afterIncrFee'"
                              :rules="dataRules.afterIncrFee" style="margin-bottom:0;">
                  <el-input clearable v-model="scope.row.afterIncrFee" type="number"
                            size="mini" style="width: 100px;"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column fixed="right">
              <template slot="header" slot-scope="scope">
                <el-button type="primary" size="mini" @click="addIncrDetails(scope.$index)">添加一行</el-button>
              </template>
              <template slot-scope="scope">
                <el-button size="mini" type="danger"
                            v-if="scope.$index === dataForm.incrDetailsList.length - 1 "
                           @click.native.prevent="deleteIncrDetails(scope.$index, dataForm.incrDetailsList)">删除
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-form-item>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="管理费标准(元/m²/月)" prop="manPricePerSquare">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.manPricePerSquare"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="电费标准(元/度)" prop="elePricePerDegree">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.elePricePerDegree"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="水费标准(元/吨)" prop="waterPricePerTon">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.waterPricePerTon"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row v-show="!this.renewNew  && dataForm.renew !== 1">
        <el-col :span="12">
          <el-form-item label="电表底数(度)" prop="electricityMeterReading">
            <el-input clearable type="number"
                      v-model.number="dataForm.electricityMeterReading"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="水表底数(吨)" prop="waterMeterReading">
            <el-input clearable type="number" v-model.number="dataForm.waterMeterReading"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="房屋押金(元)" prop="deposits">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.deposits"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label=" 其他押金(元)">
        <el-button type="primary" @click="addOtherDeposits">添加</el-button>
      </el-form-item>
      <el-form-item v-for="(item, index) in dataForm.otherDepositList" :key="'d'+index">
        <el-row>
          <el-col :span="7">
            <el-form-item :prop="'otherDepositList.'+index+'.id'"
                          :rules="dataRules.id">
              <el-select clearable filterable placeholder="请选择押金项目" v-model="item.id" style="width:240px;">
                <el-option :key="index" :label="it.name" :value="it.id"
                           v-for="(it, index) in configs.swDepositTypes"/>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="5">
            <el-form-item :prop="'otherDepositList.'+index+'.deposit'"
                          :rules="dataRules.deposit">
              <el-input-number :min="0" :precision="2" controls-position="right" v-model="item.deposit"
                               style="width: 150px;"/>
            </el-form-item>
          </el-col>
          <el-col :span="2">
            <el-button size="mini" type="danger"
                       @click.native.prevent="deleteDeposit(index, dataForm.otherDepositList)">删除
            </el-button>
          </el-col>
        </el-row>
      </el-form-item>
      <el-form-item label=" 其他收费(元)">
        <el-button type="primary" @click="addOtherFee">添加</el-button>
      </el-form-item>
      <el-form-item v-for="(item, index) in dataForm.otherFeeList" :key="'f'+index">
        <el-row>
          <el-col :span="7">
            <el-form-item :prop="'otherFeeList.'+index+'.name'"
                          :rules="dataRules.name">
              <el-input clearable v-model="item.name" placeholder="收费项目" style="width: 240px; margin-right: 10px;"/>
            </el-form-item>
          </el-col>
          <el-col :span="5">
            <el-form-item :prop="'otherFeeList.'+index+'.fee'"
                          :rules="dataRules.fee">
              <el-input-number :min="0" :precision="2" controls-position="right" v-model="item.fee" style="width: 150px;"/>
            </el-form-item>
          </el-col>
          <el-col :span="2">
            <el-button size="mini" type="danger"
                       @click.native.prevent="deleteFee(index, dataForm.otherFeeList)">删除
            </el-button>
          </el-col>
        </el-row>
      </el-form-item>
      <el-row>
        <el-col :span="12">
          <el-form-item label="是否含管理费" prop="managementIncluded">
            <template>
              <el-radio :label="1" v-model="dataForm.managementIncluded">是</el-radio>
              <el-radio :label="0" v-model="dataForm.managementIncluded">否</el-radio>
            </template>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="是否含税" prop="taxIncluded">
            <template>
              <el-radio :label="1" v-model="dataForm.taxIncluded">是</el-radio>
              <el-radio :label="0" v-model="dataForm.taxIncluded">否</el-radio>
            </template>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label="特殊条款" prop="specialTerms">
        <el-input clearable :autosize="{ minRows: 3, maxRows: 5}" type="textarea"
                  v-model="dataForm.specialTerms"/>
      </el-form-item>
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
      <el-table :data="arr" border stripe style="width: 100%" v-show="this.$route.params.id>0">
        <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无文件</span></div>
        <el-table-column prop="fileOldName" label="服务器中的文件"></el-table-column>
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
      <br><br><br><br>
      <el-form-item label-width='0px' style="padding-left: 80px">
        <el-button :disabled="sending" :loading="sending" @click="save" class="btn-save"
                   size="small" type="primary">{{ sending ? '处理中...' : '保存' }}
        </el-button>
        <el-button :disabled="sending" @click="back" size="small" type="default">取消</el-button>
        <el-button :disabled="sending2" :loading="sending2" @click="setDisable" class="btn-save"
                   size="small" type="primary" v-if="!addNew">{{ sending ? '处理中...' : '失效' }}
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import {API_SERVER, IMG_SERVER} from "../../../config";
import api from "api";
import dict from "utils/dict";
import {CodeToText, provinceAndCityData, TextToCode} from 'element-china-area-data';
import {copy, getLocalItem} from "utils/utils";

export default {
  name: "",
  data() {
    return {
      provinceAndCityList: provinceAndCityData,
      sending: false,
      sending2: false,
      renewNew: !!this.$route.params.rid,
      addNew: !this.$route.params.id,
      files: [],
      arr: [],
      showDialog: false,
      dialogLoading: false,
      newList: [],
      fileIds: [],//文件id集合
      fileStatus: 0,//文件状态，0-未上传，1-已上传
      uploadUrl: `${API_SERVER}${api.upload.uploadImg()}?x-admin-token=${getLocalItem(
        "accessToken"
      )}&fileCode=1`,
      imgServer: IMG_SERVER,
      apiServer: API_SERVER,
      uploadImagesPath:
        API_SERVER +
        "/api/rest/1.0/upload/images?x-admin-token=" +
        window.localStorage.getItem("accessToken"),
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
      dataForm: {
        rid:"",
        partyA: "",
        aProvinceAndCity: '',
        aProvince: '',
        aCity: '',
        aAddress: "",
        aLinkman: "",
        aContact: "",
        aOrgCode: "",
        partyB: "",
        partyBId: "",
        bProvinceAndCity: '',
        bProvince: '',
        bCity: '',
        bAddress: "",
        bLinkman: "",
        bContact: "",
        bOrgCode: "",
        zoneId: "",
        zoneName: "",
        buildingId: "",
        buildingName: "",
        buildingProvinceAndCity: '',
        buildingProvince: '',
        buildingCity: '',
        buildingAddress: "",
        propertyCertPic: "",
        fireApprovalPic: "",
        contractNum: "",
        leasePaymentDate: "",
        waterMeterReading: "",
        electricityMeterReading: "",
        filed: 0,
        rentFiledNum: "",
        contractDate: "",
        rentArea: "",
        rentPricePerSquare: "",
        monthlyFee: "",
        deposits: "",
        rentYears: "",
        freePeriod: 0,
        rentStartToEnd: [],
        rentStartAt: "",
        rentEndAt: "",
        freeStartToEnd: [],
        freeStartAt: "",
        freeEndAt: "",
        manPricePerSquare: "",
        elePricePerDegree: "",
        waterPricePerTon: "",
        managementIncluded: 1,
        taxIncluded: 1,
        incrementalState: 0,
        specialTerms: "",
        otherFeeList: [],
        otherDepositList: [],
        ownerId: "",
        incrDetailsList: [],
        deleteIncrDetailsList: [],
        tenancyTerm: "",
      },
      dataRules: {
        contractNum: [
          {required: true, message: "合同编号不能为空", trigger: "blur"}
        ],
        ownerId: [
          {required: true, message: "合同甲方不能为空", trigger: "blur"},
          {required: true, message: "合同甲方不能为空", trigger: "change"}
        ],
        partyBId: [
          {required: true, message: "乙方不能为空", trigger: "change"}
        ],
        /*aProvinceAndCity: [
          {required: true,message: "甲方地址不能为空",trigger: "blur"}
        ],
        bProvinceAndCity: [
          {required: true,message: "乙方地址不能为空",trigger: "blur"}
        ],
        aAddress: [
          {required: true,message: "甲方详细地址不能为空",trigger: "blur"}
        ],
        bAddress: [
          {required: true,message: "乙方详细地址不能为空",trigger: "blur"}
        ],*/
        aLinkman: [
          {required: true, message: "甲方联系人不能为空", trigger: "blur"},
          {required: true, message: "甲方联系人不能为空", trigger: "change"}
        ],
        bLinkman: [
          {required: true, message: "乙方联系人不能为空", trigger: "blur"},
          {required: true, message: "乙方联系人不能为空", trigger: "change"}
        ],
        aContact: [
          {required: true, message: "甲方联系方式不能为空", trigger: "blur"},
          {required: true, message: "甲方联系方式不能为空", trigger: "change"}
        ],
        bContact: [
          {required: true, message: "乙方联系方式不能为空", trigger: "blur"},
          {required: true, message: "乙方联系方式不能为空", trigger: "change"}
        ],
        aOrgCode: [
          {required: true, message: "甲方身份证/组织机构代码证不能为空", trigger: "blur"},
          {required: true, message: "甲方身份证/组织机构代码证不能为空", trigger: "change"}
        ],
        bOrgCode: [
          {required: true, message: "乙方组织机构代码证不能为空", trigger: "blur"},
          {required: true, message: "乙方组织机构代码证不能为空", trigger: "change"}
        ],
        zoneId: [
          {required: true, message: "区域不能为空", trigger: "change"}
        ],
        buildingId: [
          {required: true, message: "楼宇不能为空", trigger: "change"}
        ],
        /*buildingProvinceAndCity: [
          {required: true,message: "楼宇地址不能为空",trigger: "blur"}
        ],
        buildingAddress: [
          {required: true,message: "楼宇详细地址不能为空",trigger: "blur"}
        ],*/
        filed: [
          {required: true, message: "是否备案不能为空", trigger: "blur"}
        ],
        rentFiledNum: [
          {required: false, message: "租赁备案号不能为空", trigger: "blur"}
        ],
        contractDate: [
          {required: true, message: "签约日期不能为空", trigger: "blur"}
        ],
        leasePaymentDate: [
          {required: true, message: "约定交租日期不能为空", trigger: "blur"},
          {
            validator: (rule, value, callback) => {
              if (value > 28) {
                callback(new Error("约定交租日期不能大于28号"));
              }else if (value <= 0) {
                callback(new Error("约定交租日期不能小于1号"));
              }
              else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        rentArea: [
          {required: true, message: "承租面积不能为空", trigger: "blur"},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("承租面积不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        rentPricePerSquare: [
          {required: true, message: "承租单价不能为空", trigger: "blur"},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("承租单价不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        monthlyFee: [
          {required: true, message: "月租金不能为空", trigger: "blur"},
          {required: true, message: "月租金不能为空", trigger: "change"},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("月租金不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        tenancyTerm: [
          {required: true, message: "租期不能为空", trigger: "blur"},
          {required: true, message: "租期不能为空", trigger: "change"},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("租期不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        rentStartToEnd: [
          {required: true, message: "租期起始时间不能为空", trigger: "blur"}
        ],
        freePeriod: [
          {required: true, message: "是否有免租期不能为空", trigger: "blur"}
        ],
        freeStartToEnd: [
          {required: false, message: "免租期起始时间不能为空", trigger: "blur"}
        ],
        incrementalState: [
          {required: true, message: "是否有递增周期不能为空", trigger: "blur"}
        ],
        incrPeriodStartAt: [
          {required: true, message: '递增周期开始时间不能为空', trigger: 'blur'},
          {required: true, message: '递增周期开始时间不能为空', trigger: 'change'},
        ],
        incrPeriodEndAt: [
          {required: true, message: '递增周期结束时间不能为空', trigger: 'blur'}
        ],
        incrRatio: [
          {required: true, message: '递增比例不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("递增比例不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        incrUnitPrice: [
          {required: true, message: '递增单价不能为空', trigger: 'blur'},
          {required: true, message: '递增单价不能为空', trigger: 'change'}
        ],
        incrFee: [
          {required: true, message: '递增金额不能为空', trigger: 'blur'},
          {required: true, message: '递增金额不能为空', trigger: 'change'}
        ],
        afterIncrUnitPrice: [
          {required: true, message: '递增后单价不能为空', trigger: 'blur'},
          {required: true, message: '递增后单价不能为空', trigger: 'change'}
        ],
        afterIncrFee: [
          {required: true, message: '递增后金额不能为空', trigger: 'blur'},
          {required: true, message: '递增后金额不能为空', trigger: 'change'}
        ],
        manPricePerSquare: [
          {required: true, message: "管理费单价不能为空", trigger: "blur"}
        ],
        elePricePerDegree: [
          {required: true, message: "电费单价不能为空", trigger: "blur"},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("电费单价不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("电费单价不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "change"
          },
        ],
        waterPricePerTon: [
          {required: true, message: "水费单价不能为空", trigger: "blur"},
        ],
        electricityMeterReading: [
          {required: true, message: '电表底数不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value < 0) {
                callback(new Error("电表底数不能小于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        waterMeterReading: [
          {required: true, message: '水表底数不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value < 0) {
                callback(new Error("水表底数不能小于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        deposits: [
          {required: true, message: "房屋押金不能为空", trigger: "blur"}
        ],
        id: [
          {required: true, message: '其他押金项目不能为空', trigger: 'blur'},
          {required: true, message: '其他押金项目不能为空', trigger: 'change'}
        ],
        deposit: [
          {required: true, message: '其他押金金额不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("其他押金金额不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("其他押金金额不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "change"
          },
        ],
        name: [
          {required: true, message: '其他收费项目名称不能为空', trigger: 'blur'}
        ],
        fee: [
          {required: true, message: '其他收费金额不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("其他收费金额不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("其他收费金额不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "change"
          },
        ],
        managementIncluded: [
          {required: true, message: "是否含管理费不能为空", trigger: "blur"}
        ],
        taxIncluded: [
          {required: true, message: "是否含税不能为空", trigger: "blur"}
        ]
      },
      buildings: [],
      owners: [],
      companyList: [],
    };
  },
  computed: {
    configs() {
      return this.$store.getters["sys/configs"];
    }
  },
  async mounted() {
    await this.loadOwners();
    await this.loadCompany();
    if (this.renewNew) {
      this.$store.dispatch("breadcrumb/addPath", {
        title: "续签业主合同",
        action: "renewHtOwnerContract"
      });
      this.loadRenewData();
    } else if (this.addNew) {
      this.$store.dispatch("breadcrumb/addPath", {
        title: "新增业主合同",
        action: "addHtOwnerContract"
      });
      // 加载合同编号
      this.nextId();
    } else {
      this.$store.dispatch("breadcrumb/addPath", {
        title: "编辑业主合同",
        action: "editHtOwnerContract"
      });
      this.loadData();
    }
  },
  methods: {
    // 时间选择器
     changeDate(index) {
      let dataLength = this.dataForm.incrDetailsList.length
      if (dataLength - 1 > index) {
        let endTime = this.dataForm.incrDetailsList[index].incrPeriodEndAt;
        let str = endTime;
        let strList = str.split("-");
        endTime = strList[0] + '-' + strList[1] + '-' + ++strList[2];
        this.dataForm.incrDetailsList[index + 1].incrPeriodStartAt = endTime;
        this.dataForm.incrDetailsList[index + 1].pickerOptions = {
          disabledDate(time) {
            return time < new Date(endTime).getTime()
          }
        }
      }
    },
    /**
     * 递增金额计算
     * incrRatio 递增比例
     * incrUnitPrice 递增单价
     * rentFeeUnit 原租金单价
     * incrFee 递增金额
     * rentArea 出租面积
     * afterIncrUnitPrice 递增后单价
     * rentPricePerSquare 承租单价
     * @param index
     */
     changeInfo(index){
       if (index === 0) {
        let incrRatio = this.dataForm.incrDetailsList[index].incrRatio;
        this.dataForm.incrDetailsList[index].incrUnitPrice = Math.floor(this.dataForm.rentPricePerSquare * (incrRatio * 0.01) * 100) / 100
        this.dataForm.incrDetailsList[index].incrFee = +this.dataForm.incrDetailsList[index].incrUnitPrice * this.dataForm.rentArea
        this.dataForm.incrDetailsList[index].afterIncrUnitPrice = +this.dataForm.rentPricePerSquare + Number(this.dataForm.incrDetailsList[index].incrUnitPrice)
        this.dataForm.incrDetailsList[index].afterIncrFee = Math.floor(this.dataForm.incrDetailsList[index].afterIncrUnitPrice * this.dataForm.rentArea * 100) /100
       }else {
        let incrRatio = this.dataForm.incrDetailsList[index].incrRatio;
        this.dataForm.incrDetailsList[index].incrUnitPrice = Math.floor(+this.dataForm.incrDetailsList[index-1].afterIncrUnitPrice * (incrRatio * 0.01)* 100) / 100
        this.dataForm.incrDetailsList[index].incrFee = +this.dataForm.incrDetailsList[index].incrUnitPrice * this.dataForm.rentArea
        this.dataForm.incrDetailsList[index].afterIncrUnitPrice = +this.dataForm.rentPricePerSquare + Number(this.dataForm.incrDetailsList[index].incrUnitPrice)
        this.dataForm.incrDetailsList[index].afterIncrFee = Math.floor(this.dataForm.incrDetailsList[index].afterIncrUnitPrice * this.dataForm.rentArea * 100) / 100
       }
     },
    setFormDate(){
      const length = this.dataForm.incrDetailsList.length
      for (let index = 0; index < length; index++) {
        this.changeInfo(index)
      }
    },

    //加载编辑数据
    async loadData() {
      //获取当前页已同步的文件
      const res = await api.upload.getFile(this.$route.params.id, this.FileList.businessCode);
      this.files.push(res.data)
      if (res.code === 200) {
        this.files.forEach((item) => {
          for (let i = 0; i < item.length; i++) {
            this.arr.push(item[i]);
            copy(this.FileList, item[i]);
          }
        })
        if (this.arr.length > 0) {
          this.fileStatus = 1;
        }
      }
      const result = await api.htOwnerContract.getById(this.$route.params.id);
      if (result.code == 200) {
        await this.loadBuildings(result.data.zoneId);
        this.dataForm = result.data;
        console.log(result.data);
        if (this.dataForm.renew === 1) {
          this.dataRules.electricityMeterReading[0].required = false;
          this.dataRules.electricityMeterReading[1].required = false;

          this.dataRules.waterMeterReading[0].required = false;
          this.dataRules.waterMeterReading[1].required = false;
        }
        this.dataForm.partyBId = Number(this.dataForm.partyBId);
        this.dataForm.otherFeeList = this.dataForm.otherFeeList != null ? this.dataForm.otherFeeList : []
        this.dataForm.otherDepositList = this.dataForm.otherDepositList != null ? this.dataForm.otherDepositList : []
        if (this.dataForm.incrDetailsList && this.dataForm.incrDetailsList.length > 0) {
          this.dataForm.incrDetailsList.forEach(incr => {
            this.$set(incr, 'incrPeriodAt', [incr.incrPeriodStartAt, incr.incrPeriodEndAt])
          })
        } else {
          this.dataForm.incrDetailsList = []
        }
        if (this.dataForm.aProvince != null && this.dataForm.aProvince.trim().length !== 0 && this.dataForm.aCity != null && this.dataForm.aCity.trim().length !== 0) {
          this.dataForm.aProvinceAndCity = [TextToCode[this.dataForm.aProvince].code, TextToCode[this.dataForm.aProvince][this.dataForm.aCity].code];
        }
        if (this.dataForm.bProvince != null && this.dataForm.bProvince.trim().length !== 0 && this.dataForm.bCity != null && this.dataForm.bCity.trim().length !== 0) {
          this.dataForm.bProvinceAndCity = [TextToCode[this.dataForm.bProvince].code, TextToCode[this.dataForm.bProvince][this.dataForm.bCity].code];
        }
        if (this.dataForm.buildingProvince != null && this.dataForm.buildingProvince.trim().length !== 0 && this.dataForm.buildingCity != null && this.dataForm.buildingCity.trim().length !== 0) {
          this.dataForm.buildingProvinceAndCity = [TextToCode[this.dataForm.buildingProvince].code, TextToCode[this.dataForm.buildingProvince][this.dataForm.buildingCity].code];
        }
        this.$set(this.dataForm, 'rentStartToEnd', [this.dataForm.rentStartAt, this.dataForm.rentEndAt])
        if (this.dataForm.freePeriod === 1) {
          this.$set(this.dataForm, 'freeStartToEnd', [this.dataForm.freeStartAt, this.dataForm.freeEndAt])
          this.dataRules.freeStartToEnd[0].required = true;
        } else {
          this.$set(this.dataForm, 'freeStartToEnd', [])
          this.dataRules.freeStartToEnd[0].required = false;
        }
      } else {
        this.$message.error(result.msg);
      }
    },

    //加载续签数据
    async loadRenewData() {
      const result = await api.htOwnerContract.getById(this.$route.params.rid);
      if (result.code == 200) {
        const reDate = result.data
        this.dataForm = reDate;
        // 加载合同编号
        this.nextId();
        this.dataForm.rid = this.$route.params.rid;
        if (reDate.aProvince != null && reDate.aProvince.trim().length !== 0 && reDate.aCity != null && reDate.aCity.trim().length !== 0) {
          this.dataForm.aProvinceAndCity = [TextToCode[reDate.aProvince].code, TextToCode[reDate.aProvince][reDate.aCity].code];
        }
        this.dataForm.partyBId = Number(reDate.partyBId);
        if (reDate.bProvince != null && reDate.bProvince.trim().length !== 0 && reDate.bCity != null && reDate.bCity.trim().length !== 0) {
          this.dataForm.bProvinceAndCity = [TextToCode[reDate.bProvince].code, TextToCode[reDate.bProvince][reDate.bCity].code];
        }
        await this.loadBuildings(reDate.zoneId);
        if (reDate.buildingProvince != null && reDate.buildingProvince.trim().length !== 0 && reDate.buildingCity != null && reDate.buildingCity.trim().length !== 0) {
          this.dataForm.buildingProvinceAndCity = [TextToCode[reDate.buildingProvince].code, TextToCode[reDate.buildingProvince][reDate.buildingCity].code];
        }
        this.dataForm.rentStartToEndpickerOptions = {
          disabledDate(time) {
            return time.getTime() < new Date(reDate.rentEndAt)
          }
        }
        //清除部分原合同数据
        this.dataForm.freePeriod = 0;
        this.dataForm.filed = 0;
        this.dataForm.incrementalState = 0;
        this.dataForm.incrDetailsList = [];
        this.dataForm.otherFeeList = [];
        this.dataForm.otherDepositList = [];
        this.dataForm.tenancyTerm = "";
        this.dataForm.specialTerms = "";
        this.dataForm.contractDate = "";
        this.dataForm.leasePaymentDate = "";
        //清除水电表底数校验
        this.dataRules.electricityMeterReading[0].required = false;
        this.dataRules.electricityMeterReading[1].required = false;
        this.dataRules.waterMeterReading[0].required = false;
        this.dataRules.waterMeterReading[1].required = false;
      }
    },

    //保存
    async save() {
      this.$refs.dataForm.validate(async valid => {
        if (valid) {
          if (this.dataForm.otherDepositList.length > 0) {
            this.dataForm.otherDepositList.forEach(otherDeposit => {
              otherDeposit.name = dict.description(
                this.configs.swDepositTypes,
                otherDeposit.id
              );
            })
          }
          if (this.dataForm.incrementalState === 0) {
            if (this.dataForm.deleteIncrDetailsList === undefined || this.dataForm.deleteIncrDetailsList === null) {
              this.dataForm.deleteIncrDetailsList = []
            }
            if (this.dataForm.incrDetailsList && this.dataForm.incrDetailsList.length > 0) {
              this.dataForm.incrDetailsList.forEach(incr => {
                if (incr.id != null) {
                  this.dataForm.deleteIncrDetailsList.push(incr)
                }
              })
            }
            this.dataForm.incrDetailsList = []
          }
          if (this.dataForm.filed === 0) {
            this.dataForm.rentFiledNum = "";
          }
          if (this.dataForm.freePeriod === 1) {
            this.dataForm.freeStartAt = this.dataForm.freeStartToEnd[0]
            this.dataForm.freeEndAt = this.dataForm.freeStartToEnd[1]
          } else {
            this.dataForm.freeStartAt = "";
            this.dataForm.freeEndAt = "";
          }
          this.dataForm.aProvince = CodeToText[this.dataForm.aProvinceAndCity[0]];
          this.dataForm.aCity = CodeToText[this.dataForm.aProvinceAndCity[1]];
          this.dataForm.bProvince = CodeToText[this.dataForm.bProvinceAndCity[0]];
          this.dataForm.bCity = CodeToText[this.dataForm.bProvinceAndCity[1]];
          this.dataForm.buildingProvince = CodeToText[this.dataForm.buildingProvinceAndCity[0]];
          this.dataForm.buildingCity = CodeToText[this.dataForm.buildingProvinceAndCity[1]];
          this.dataForm.rentStartAt = this.dataForm.rentStartToEnd[0]
          this.dataForm.rentEndAt = this.dataForm.rentStartToEnd[1]
          this.sending = true;
          let params = Object.assign({}, this.dataForm);
          params.zoneName = dict.description(
            this.configs.zones,
            this.dataForm.zoneId
          );
          if (this.fileIds.length) {
            params.fileIds = this.fileIds
          }
          if (this.fileStatus === 1) {
            this.sending = false;
            const result =
              await api.htOwnerContract[this.renewNew ? "renew" : (this.addNew ? "add" : "updateById")](params);
            const tip = this.renewNew ? "续签成功" : (this.$route.params.id ? "更新成功" : "新增成功");
            if (result.code == 200) {
              this.$message.success(tip);
              this.$router.go(-1);
            } else {
              this.$message.error(result.msg);
            }
          } else {
            this.sending = false;
            this.$message.warning("请上传已选择的文件！");
          }
        } else {
          this.$message.error("请检查必填项是否全部填写");
          return false;
        }
      });
    },

    //合同甲方
    async loadOwners() {
      const result = await api.owners.all({});
      if (result.code === 200) {
        this.owners = result.data;
      } else {
        this.$message.error(result.msg);
      }
    },
    selectOwner(id) {
      if (id) {
        if (this.owners.length) {
          this.owner = this.owners.find(item => {
            return item.id == id;
          });
          this.dataForm.partyA = this.owner.ownerName;
          this.dataForm.aLinkman = this.owner.linkman;
          this.dataForm.aContact = this.owner.contactMobile;
          if (this.owner.province != null && this.owner.province.trim().length != 0 && this.owner.city != null && this.owner.city.trim().length != 0) {
            this.dataForm.aProvinceAndCity = [TextToCode[this.owner.province].code, TextToCode[this.owner.province][this.owner.city].code];
          }
          this.dataForm.aAddress = this.owner.linkAddress;
          this.dataForm.aOrgCode = this.owner.orgCode;
        }
      } else {
        this.dataForm.ownerId = "";
        this.dataForm.partyA = "";
        this.dataForm.aLinkman = "";
        this.dataForm.aContact = "";
        this.dataForm.aProvinceAndCity = [];
        this.dataForm.aAddress = "";
        this.dataForm.aOrgCode = "";
      }
    },

    //合同乙方 公司
    async loadCompany() {
      const result = await api.htOwnerContract.getCompany();
      if (result.code === 200) {
        this.companyList = result.data;
      } else {
        this.$message.error(result.msg);
      }
    },
    selectCompany(id) {
      if (id) {
        if (this.companyList) {
          const obj = this.companyList.find(item => {
            return item.id === id;
          });
          if (!obj) {
            return;
          }
          this.dataForm.partyB = obj.companyName;
          if (obj.province != null && obj.province.trim().length != 0 && obj.city != null && obj.city.trim().length != 0) {
            this.dataForm.bProvinceAndCity = [TextToCode[obj.province].code, TextToCode[obj.province][obj.city].code];
          }
          this.dataForm.bAddress = obj.detailedAddress;
          this.dataForm.bLinkman = obj.linkman;
          this.dataForm.bContact = obj.linkPhone;
          this.dataForm.bOrgCode = obj.organizationCode;
        }
      } else {
        this.dataForm.partyB = "";
        this.dataForm.bProvinceAndCity = [];
        this.dataForm.bAddress = "";
        this.dataForm.bLinkman = "";
        this.dataForm.bContact = "";
        this.dataForm.bOrgCode = "";
      }
    },

    //区域
    selectZone(id) {
      if (id) {
        if (this.configs.zones) {
          const obj = this.configs.zones.find(item => {
            return item.id === id;
          });
          if (!obj) {
            return;
          }
          this.dataForm.zoneName = obj.name;
          //this.dataForm.buildingId = "";
          this.loadBuildings(id);
          this.selectBuilding();
        }
      } else {
        this.dataForm.zoneId = "";
        this.dataForm.zoneName = "";
        this.dataForm.buildingId = "";
        this.dataForm.buildingName = "";
        this.dataForm.buildingProvinceAndCity = [];
        this.dataForm.buildingAddress = "";
        this.dataForm.roomId = "";
        this.dataForm.roomNum = "";
        this.buildings = [];
        this.selectBuilding();
      }
    },

    //楼宇
    async loadBuildings(zoneId) {
      this.buildings = [];
      if (zoneId) {
        const params = Object.assign(
          {
            auditStateId: 2,
            zoneId: zoneId
          },
        );
        this.loading = true;
        const res = await api.evaluate.listAll(params)
        if (res.code === 200) {
          let buildings = []
          res.data.forEach(building => {
            building.value = building.building;
            buildings.push(building);
            this.buildings = buildings;
          });
        }
      }
    },
    selectBuilding(id) {
      if (id) {
        const obj = this.buildings.find(item => {
          return item.buildingId === id;
        });
        if (obj.province != null && obj.province.trim().length != 0 && obj.city != null && obj.city.trim().length != 0) {
          this.dataForm.buildingProvinceAndCity = [TextToCode[obj.province].code, TextToCode[obj.province][obj.city].code];
        }
        this.dataForm.buildingName = obj.building;
        this.dataForm.buildingAddress = obj.address;//楼宇地址
        this.dataForm.tenancyTerm = obj.tenancyTerm;//租期
        this.dataForm.rentPricePerSquare = obj.rentPricePerSquare;//承租单价
        this.dataForm.manPricePerSquare = obj.manTaxFee;//管理费标准
        this.dataForm.rentArea = obj.rentArea;//承租面积
        this.dataForm.monthlyFee = obj.rentArea * obj.rentPricePerSquare;//月租金
        this.dataForm.deposits = obj.deposits;//房屋押金
      } else {
        this.dataForm.buildingId = "";
        this.dataForm.buildingName = "";
        this.dataForm.buildingProvinceAndCity = [];
        this.dataForm.buildingAddress = "";
        this.dataForm.tenancyTerm = "";
        this.dataForm.rentPricePerSquare = "";//承租单价
        this.dataForm.manPricePerSquare = "";//管理费标准
        this.dataForm.rentArea = "";//承租面积
        this.dataForm.monthlyFee = "";//月租金
        this.dataForm.deposits = "";//房屋押金
      }
    },

    //备案
    setFiledReq(value) {
      if (value === 1) {
        this.dataRules.rentFiledNum[0].required = true;
      } else {
        this.dataRules.rentFiledNum[0].required = false;
      }
    },

    //免租期
    setFreePeriodReq(value) {
      this.dataRules.freeStartToEnd[0].required = value === 1;
    },

    //递增周期
    addIncrDetails(index) {
      let current = this.dataForm.incrDetailsList[ this.dataForm.incrDetailsList.length - 1]
      let endTime = "";
      if (current) {
        let str = current.incrPeriodEndAt;
        let strList = str.split("-");
        endTime = strList[0] + '-' + strList[1] + '-' + ++strList[2];
      }
      this.dataForm.incrDetailsList.push({
        incrPeriodAt: [],
        incrPeriodStartAt:  current ?  endTime : '',
        incrPeriodEndAt: "",
        incrRatio: "",//递增比例
        incrUnitPrice: "",//递增单价
        incrFee: "",//递增金额
        afterIncrUnitPrice: "",//递增后单价
        afterIncrFee: "",//递增后金额
        pickerOptions: {
        disabledDate (time) {
          if (!current) {
            return
          }
          return time <   new Date(  current.incrPeriodEndAt).getTime()
        }
      }
      })
      let data  =  this.dataForm.incrDetailsList
      data[data.length - 1 ].incrPeriodEndAt &&  this.changeDate(index)
    },
    deleteIncrDetails(index, rows) {
      if (this.dataForm.deleteIncrDetailsList === undefined || this.dataForm.deleteIncrDetailsList === null) {
        this.dataForm.deleteIncrDetailsList = []
      }
      if (rows[index].id != null) {
        this.dataForm.deleteIncrDetailsList.push(rows[index])
      }
      rows.splice(index, 1);
    },

    //其他押金
    addOtherDeposits() {
      this.dataForm.otherDepositList.push({
        id: "",
        name: "",
        deposit: 0,
      });
    },
    deleteDeposit(index, rows) {
      rows.splice(index, 1);
    },

    //其他收费
    addOtherFee() {
      this.dataForm.otherFeeList.push({
        name: "",
        fee: 0,
      });
    },
    deleteFee(index, rows) {
      rows.splice(index, 1);
    },

    //月租金
    calrent() {
      this.dataForm.monthlyFee = this.dataForm.rentArea * this.dataForm.rentPricePerSquare;
      this.setFormDate()
    },

    //合同编号
    async nextId() {
      this.dataForm.contractNum = await api.tools.nextId("HT");
    },

    //失效
    async setDisable() {
      const params = {
        id: this.dataForm.id,
        contractStateId: 3,
        contractState: "已失效"
      };
      this.sending2 = true;
      const result = await api.htOwnerContract.updateById(params);
      if (result.code == 200) {
        this.$message.success("操作成功");
        this.$router.go(-1);
      } else {
        this.$message.error(result.msg);
      }
    },

    //返回
    back() {
      this.$router.go(-1);
    },

    add() {
      this.$router.push({name: "addHtOwnerContract"});
    },

    //上传文件成功后的钩子
    handleAvatarSuccess(res, file) {
      if (res.code === 200) {
        this.$message.success('上传成功!');
        this.fileIds.push(res.data.fileId);
        this.fileStatus = 1;
        this.dataForm.propertyCertPic = `${res.data.assertUrl}`;
        this.dataForm.fireApprovalPic = `${res.data.assertUrl}`;
        //this.dataForm.propertyCertPic = `data:image/png;base64,${res.data.imgBase}`;
      }
    },
    submitUpload(file) {
      this.$refs.upload.submit();
    },
    handleChange(file) {
    },
    //下载文件
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
    //查看文件附件
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

  },
};
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  .avatar-uploader .el-upload:hover {
    border-color: #409eff;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }

  .avatar {
    width: 300px;
    height: 240px;
    display: block;
  }

  .el-col-2 {
    width: 148px;
  }

  .el-col-1 {
    width: 9px;
  }
}
</style>
