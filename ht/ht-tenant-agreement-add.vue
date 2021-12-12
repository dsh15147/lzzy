<template>
  <div class="main-content">
    <el-form :model="dataForm" :rules="dataRules" class="data-form" label-position="left"
             label-width="200px" ref="dataForm" size="small">
      <el-row>
        <el-col :span="24">
          <el-form-item label="租赁协议编号">
            <el-col :span="6">
              <el-form-item prop="buildingAbbreviation">
                <el-input clearable v-model="dataForm.buildingAbbreviation" style="width: 100%" readonly
                          placeholder="选择楼宇后自动带出楼宇简称"/>
                <!--                <el-tooltip class="item" effect="light" content="选择楼宇后自动带出楼宇简称" placement="top-end">
                                  <i class="el-icon-question"/>
                                </el-tooltip>-->
              </el-form-item>
            </el-col>
            <el-col class="line" :span="1">-</el-col>
            <el-col :span="6">
              <el-form-item prop="agreementNumberDate">
                <el-date-picker type="date" v-model="dataForm.agreementNumberDate" style="width: 100%;"
                                format="yyyyMMdd" value-format="yyyyMMdd"
                                placeholder="日期"/>
              </el-form-item>
            </el-col>
            <el-col class="line" :span="1">-</el-col>
            <el-col :span="6">
              <el-form-item prop="agreementNumberIndex">
                <el-input-number v-model="dataForm.agreementNumberIndex" controls-position="right" :min="1" :max="999"
                                 style="width: 100%" placeholder="序号"/>
              </el-form-item>
            </el-col>
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
          <el-form-item label="房号" prop="roomIdList">
            <el-select multiple filterable v-model="dataForm.roomIdList" placeholder="请选择房号"
                       @change="selectRoom" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="index" :label="item.roomNum" :value="item.id" v-for="(item, index) in rooms"/>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-form-item label="仪表底数" label-width="100px">
          <el-table :data="dataForm.meters" style="width: 100%;margin-right: 10%" stripe border>
            <el-table-column label="序号" width="50px" type="index"/>
            <el-table-column label="房间号" width="120px">
              <template slot-scope="scope">{{ scope.row.roomNum }}</template>
            </el-table-column>
            <el-table-column label="仪表编号" width="150px">
              <template slot-scope="scope">{{ scope.row.meterNumber }}</template>
            </el-table-column>
            <el-table-column label="仪表名称" width="150px">
              <template slot-scope="scope">{{ scope.row.meterName }}</template>
            </el-table-column>
            <el-table-column label="仪表类型" width="120px">
              <template slot-scope="scope">{{ scope.row.meterType }}</template>
            </el-table-column>
            <el-table-column label="仪表底数">
              <template slot-scope="scope">
                <el-form-item :validate-status="scope.row.switch?'1':'0'" :prop="'meters.'+scope.$index+'.meteBase'"
                              :rules="scope.row.switch?dataRules.meteBase:[]">
                  <el-input clearable v-model.number="scope.row.meteBase" type="number" size="mini" style="width: 150px;" :disabled="scope.row.inputDisabled"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="启用状态">
              <template slot-scope="scope">
                <el-switch v-model="scope.row.switch"
                           active-color="#13ce66" inactive-color="#ff4949"
                           active-text="启用" inactive-text="禁用">
                </el-switch>
              </template>
            </el-table-column>
          </el-table>
        </el-form-item>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="出租方(甲方)" prop="partyAId">
            <el-select clearable filterable v-model="dataForm.partyAId" placeholder="请选择甲方"
                       @change="selectCompany" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="index" :label="item.companyName" :value="item.id"
                         v-for="(item, index) in companyList"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="承租方(乙方)" prop="tenantId">
            <el-select clearable filterable placeholder="请选择承租方(乙方)" v-model="dataForm.tenantId"
                       @change="selectTenant" :disabled="this.renewNew || dataForm.renew === 1">
              <el-option :key="index" :label="item.tenantName" :value="item.id"
                         v-for="(item, index) in tenants"/>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方地址" prop="aProvinceAndCity">
            <el-cascader clearable :options="provinceAndCityList" v-model="dataForm.aProvinceAndCity"
                         placeholder="选择出租方(甲方)后自动带出" disabled/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方地址" prop="tenantProvinceAndCity">
            <el-cascader clearable :options="provinceAndCityList" v-model="dataForm.tenantProvinceAndCity"
                         placeholder="选择承租方(乙方)后自动带出" disabled/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方详细地址" prop="aAddress">
            <el-input clearable v-model="dataForm.aAddress"
                      placeholder="选择出租方(甲方)后自动带出" disabled/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方详细地址" prop="tenantAddress">
            <el-input clearable v-model="dataForm.tenantAddress"
                      placeholder="选择承租方(乙方)后自动带出" disabled/>
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
          <el-form-item label="乙方联系人" prop="contractor">
            <el-input clearable v-model="dataForm.contractor"/>
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
          <el-form-item label="乙方联系方式" prop="contractorMobile">
            <el-input clearable v-model="dataForm.contractorMobile"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="甲方组织机构代码证" prop="aOrgCode">
            <el-input clearable v-model="dataForm.aOrgCode"
                      placeholder=""/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方身份证/组织机构代码证" prop="tenantOrgCode">
            <el-input clearable v-model="dataForm.tenantOrgCode"
                      placeholder=""/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="乙方公司法人" prop="corpRepresentative" v-if="dataForm.tenantTypeId === 1">
            <el-input clearable v-model="dataForm.corpRepresentative"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="乙方经营类型" prop="managementTypeId">
            <el-select clearable filterable placeholder="请选择经营类型" v-model="dataForm.managementTypeId">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.managementTypes"/>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-form-item label="甲方收款账户">
          <el-table :data="dataForm.accountList" style="width: 100%" stripe border>
            <el-table-column label="收款账户" width="330px">
              <template slot-scope="scope">
                <el-form-item :prop="'accountList.'+scope.$index+'.id'"
                              :rules="dataRules.id">
                  <el-select clearable filterable placeholder="请选择收款账户" v-model="scope.row.id"
                             @change="selectAccount(scope.$index, dataForm.accountList)">
                    <el-option :key="index" :label="it.accountName" :value="it.id"
                               v-for="(it, index) in accounts"/>
                  </el-select>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="开户行" width="200px">
              <template slot-scope="scope">
                <el-form-item :prop="'accountList.'+scope.$index+'.accountBank'"
                              :rules="dataRules.accountBank">
                  <el-input clearable v-model="scope.row.accountBank" size="mini" style="width: 175px"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="账号" width="200px">
              <template slot-scope="scope">
                <el-form-item :prop="'accountList.'+scope.$index+'.accountNumber'"
                              :rules="dataRules.accountNumber">
                  <el-input clearable v-model="scope.row.accountNumber" size="mini" style="width: 175px"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column>
              <template slot="header" slot-scope="scope">
                <el-button type="primary" size="mini" @click="addAccount">添加一行</el-button>
              </template>
              <template slot-scope="scope">
                <el-button size="mini" type="danger"
                           @click.native.prevent="deleteAccount(scope.$index, dataForm.accountList)">删除
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-form-item>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="签约日期" prop="contractDate">
            <el-date-picker clearable format="yyyy-MM-dd" placeholder="" type="date" v-model="dataForm.contractDate"
                            value-format="yyyy-MM-dd"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="约定交租日期" prop="leasePaymentDate">每月
            <el-input clearable v-model="dataForm.leasePaymentDate" style="width: 150px;"></el-input>
            日
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="协议租期" prop="agreementStartToEnd">
            <el-date-picker v-model="dataForm.agreementStartToEnd" type="daterange" range-separator="至"
                            start-placeholder="协议起租日" value-format="yyyy-MM-dd"
                            end-placeholder="协议到期日" size="mini"
                            :picker-options="dataForm.agreementStartToEndpickerOptions"
                            :default-value="dataForm.agreementStartToEndDefaultValue">
            </el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="备案租期" prop="contractStartToEnd">
            <el-date-picker v-model="dataForm.contractStartToEnd" type="daterange" range-separator="至"
                            start-placeholder="备案起租日" value-format="yyyy-MM-dd"
                            end-placeholder="备案到期日" size="mini">
            </el-date-picker>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="租赁凭证编号" prop="ticketNumber">
            <el-input clearable v-model="dataForm.ticketNumber"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="出租面积(m²)" prop="rentArea">
            <el-input-number :min="0" :precision="2" controls-position="right"
                             v-model="dataForm.rentArea" @change="setMonthlyRentFee"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="备案面积(m²)" prop="regArea">
            <el-input-number :min="0" :precision="2" controls-position="right" v-model="dataForm.regArea"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="租赁类型" prop="leaseTypeId">
            <el-select clearable filterable placeholder="请选择租赁类型" v-model="dataForm.leaseTypeId">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.leaseTypes"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="协议类型" prop="agreementTypeId">
            <el-select clearable filterable placeholder="请选择协议类型" v-model="dataForm.agreementTypeId">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.agreementTypes"/>
            </el-select>
            <el-input v-if="dataForm.agreementTypeId===5" v-model="dataForm.agreementTypeIdName"/>
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
      <el-row>
        <el-col :span="12">
          <el-form-item label="空调费计算方式" prop="airConditionFeesMethodId">
            <el-select clearable filterable placeholder="请选择空调费计算方式" v-model="dataForm.airConditionFeesMethodId"
                       @change="cleanAirConditionFees">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.airConditionFeesType"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="计费单价" prop="airConditionFeesUnitPrice"
                        v-if="dataForm.airConditionFeesMethodId === 2 || dataForm.airConditionFeesMethodId === 3">
            <el-input type="number" v-model.number="dataForm.airConditionFeesUnitPrice"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="租金单价(元/m²/月)" prop="rentFeeUnit">
            <el-input type="number" v-model.number="dataForm.rentFeeUnit" @change="setMonthlyRentFee"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="月租金(元/月)" prop="monthlyRentFee">
            <el-input type="number" v-model.number="dataForm.monthlyRentFee" @change="setDeposits"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="租金收取方式" prop="rentCollectionMethodId">
            <el-select clearable filterable placeholder="请选择租金收取方式" v-model="dataForm.rentCollectionMethodId">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.rentCollectionMethods"/>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="房屋押金类型" prop="depositsTypeId">
            <el-select clearable filterable placeholder="请选择房屋押金类型" v-model="dataForm.depositsTypeId"
                       @change="setDeposits">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.depositMultiple"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="房屋押金" prop="deposits">
            <el-input type="number" clearable v-model.number="dataForm.deposits"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="有无免租期" prop="freePeriod">
            <el-radio-group v-model="dataForm.freePeriod" @change="cleanFreePeriod">
              <el-radio :label="1">有</el-radio>
              <el-radio :label="0">无</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="免租期" prop="freeStartToEnd" v-if="dataForm.freePeriod === 1">
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
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-form-item label="递增周期" label-width="300" v-if="dataForm.incrementalState === 1">
          <el-table :data="dataForm.incrDetailsList" style="width: 100%" stripe border>
            <el-table-column label="递增区间" width="500px">
              <template slot-scope="scope">
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
                                    type="date" style=" width: 200px;"
                                    placeholder="结束日期" format="yyyy-MM-dd"
                                    value-format="yyyy-MM-dd" @change="changeDate(scope.$index)"/>
                    <el-date-picker v-else v-model="scope.row.incrPeriodEndAt"
                                    type="date" style=" width: 200px;"
                                    placeholder="结束日期" format="yyyy-MM-dd"
                                    value-format="yyyy-MM-dd" :picker-options="scope.row.pickerOptions"
                                    @change="changeDate(scope.$index)"/>
                  </el-form-item>
                </el-col>
              </template>
            </el-table-column>
            <el-table-column label="递增比例(%)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrRatio'"
                              :rules="dataRules.incrRatio" style="margin-bottom:0;">
                  <el-input clearable @input="changeInfo(scope.$index)" v-model="scope.row.incrRatio" type="number"
                            size="mini" style="width: 100px;"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="递增单价(元/m²/月)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrUnitPrice'"
                              :rules="dataRules.incrUnitPrice" style="margin-bottom:0;">
                  <el-input clearable v-model="scope.row.incrUnitPrice" type="number" size="mini" style="width: 100px;"/>
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column label="递增金额(元)" width="160px">
              <template slot-scope="scope">
                <el-form-item :prop="'incrDetailsList.'+scope.$index+'.incrFee'"
                              :rules="dataRules.incrFee" style="margin-bottom:0;">
                  <el-input clearable v-model="scope.row.incrFee" type="number" size="mini" style="width: 100px;"/>
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
                  <el-input clearable v-model="scope.row.afterIncrFee" type="number" size="mini" style="width: 100px;"/>
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
        <el-form-item label="其他押金(元)">
          <el-button type="primary" @click="addOtherDeposits">添加</el-button>
        </el-form-item>
        <el-form-item v-for="(item, index) in dataForm.otherDepositList" :key="'d'+index">
          <el-row>
            <el-col :span="7">
              <el-form-item :prop="'otherDepositList.'+index+'.id'"
                            :rules="dataRules.aid">
                <el-select clearable filterable placeholder="请选择押金项目" v-model="item.id" style="width: 240px;">
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
      </el-row>
      <el-row>
        <el-form-item label="是否带装修" prop="decorationStatus">
          <el-radio-group v-model="dataForm.decorationStatus">
            <el-radio :label="1">是</el-radio>
            <el-radio :label="0">否</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="是否包含管理费" prop="managementIncluded">
            <el-radio-group v-model="dataForm.managementIncluded" @change="cleanManagementFees">
              <el-radio :label="1">是</el-radio>
              <el-radio :label="0">否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="管理费" prop="managementFees" v-if="dataForm.managementIncluded === 0">
            <el-input v-model="dataForm.managementFees"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="是否包含税" prop="taxIncluded">
            <el-radio-group v-model="dataForm.taxIncluded" @change="cleanTaxesFees">
              <el-radio :label="1">是</el-radio>
              <el-radio :label="0">否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="税费" prop="taxesFees" v-if="dataForm.taxIncluded === 0">
            <el-input v-model="dataForm.taxesFees"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="成交信息来源" prop="tenantSourceId">
            <el-select clearable filterable placeholder="请成交信息来源" v-model="dataForm.tenantSourceId">
              <el-option :key="index" :label="item.name" :value="item.id"
                         v-for="(item, index) in configs.sourceTypes"/>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="地产公司" prop="estCompany" v-if="dataForm.tenantSourceId===1">
            <el-input clearable v-model="dataForm.estCompany"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="地产销售" prop="estSalesman" v-if="dataForm.tenantSourceId===1">
            <el-input clearable v-model="dataForm.estSalesman"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="地产销售手机号" prop="estSlaesmanMobile" v-if="dataForm.tenantSourceId===1">
            <el-input clearable v-model="dataForm.estSlaesmanMobile"/>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="备注" prop="remark">
            <el-input clearable :autosize="{ minRows: 3, maxRows: 5}" placeholder=""
                      type="textarea" v-model="dataForm.remark"/>
          </el-form-item>
        </el-col>
      </el-row>
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
      <el-form-item label="" id="SaveCancel">
        <el-button :disabled="sending" @click="back" size="small" type="default">取消</el-button>
        <el-button :disabled="sending" :loading="sending" @click="save" class="btn-save"
                   size="small" type="primary">{{ sending ? '处理中...' : '保存' }}
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import api from 'api'
import dict from 'utils/dict'
import {provinceAndCityData, CodeToText, TextToCode} from 'element-china-area-data';
import {API_SERVER} from "../../../config";
import {IMG_SERVER} from "../../../config";
import {getLocalItem, copy} from "utils/utils";

export default {
  name: '',
  data() {
    return {
      provinceAndCityList: provinceAndCityData,
      corpRepresentativeDisabled: false,
      sending: false,
      renewNew: !!this.$route.params.rid,
      addNew: !this.$route.params.id,
      tenants: [],
      companyList: [],
      buildings: [],
      rooms: [],
      arr: [],
      files: [],
      fileIds: [],
      fileStatus: 0,
      uploadUrl: `${API_SERVER}${api.upload.uploadImg()}?x-admin-token=${getLocalItem(
        "accessToken"
      )}&fileCode=3`,
      imgServer: IMG_SERVER,
      apiServer: API_SERVER,
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
      selectRoomId: [],//选择的房间ID
      accounts: [],//甲方账户列表
      ss: 0,
      dataForm: {
        rid:"",
        buildingAbbreviation: '',//协议编号 - 楼宇简称
        agreementNumberDate: '',//协议编号 - 时间
        agreementNumberIndex: '',//协议编号 - 编号
        tenantTypeId: '',//乙方
        tenantId: '',//乙方
        tenantName: '',//乙方名称
        corpRepresentative: '',//乙方公司法人
        tenantOrgCode: '',//乙方组织机构代码或身份证号
        tenantProvinceAndCity: '',//乙方-省市
        tenantProvince: '',//乙方-省
        tenantCity: '',//乙方-市
        tenantAddress: '',//乙方地址
        contractor: '',//乙方联系人
        contractorMobile: '',//乙方联系方式
        managementTypeId: '',//乙方经营类型
        managementType: '',//乙方经营类型
        partyAId: '',//甲方
        partyA: '',//甲方
        aLinkman: '',//甲方联系人
        aContact: '',//甲方联系方式
        aProvinceAndCity: '',//甲方-省市
        aProvince: '',//甲方-省
        aCity: '',//甲方-市
        aAddress: '',//甲方地址
        aOrgCode: '',//甲方组织机构代码
        zoneId: '',//区域
        zoneName: '',//区域
        buildingId: '',//楼宇编号
        buildingName: '',//楼宇名称
        buildingProvinceAndCity: '',//楼宇-省市
        buildingProvince: '',//楼宇-省
        buildingCity: '',//楼宇-市
        buildingAddress: '',//楼宇地址
        roomIdList: [],//房间ID
        roomNumList: [],//房号
        meterList: [],//仪表
        meters: [],//仪表
        contractDate: '',//签约日期
        leasePaymentDate: '',//约定交租日期
        agreementNumber: '',//租赁协议编号
        ticketNumber: '',//租赁凭证编号
        contractStartAt: '',//备案租期开始
        contractEndAt: '',//备案租期结束
        freeStartAt: '',//免租期开始
        freeEndAt: '',//免租期结束
        incrementalState: 0,//有无递增周期
        agreementStartAt: '',//协议租期开始
        agreementEndAt: '',//协议租期结束
        rentArea: 0,//出租面积
        regArea: 0,//备案面积
        leaseTypeId: '',//租赁类型
        leaseType: '',//租赁类型
        agreementTypeId: '',//协议类型
        agreementType: '',//协议类型
        depositsType: '',//房屋押金类型
        depositsTypeId: "",//房屋押金类型
        deposits: "",//房屋押金
        decorationStatus: 1,//是否带装修
        otherDepositList: [],//其他押金
        accountList: [],//甲方收款账户
        rentFeeUnit: "",//租金单价
        monthlyRentFee: "",//月租金
        rentCollectionMethodId: '',//租金收取方式
        rentCollectionMethod: '',//租金收取方式
        managementIncluded: 1,//是否包含管理费
        managementFees: '',//管理费
        freePeriod: 0,//是否有免租期
        taxIncluded: 1,//是否包含税费
        taxesFees: '',//税费
        tenantSourceId: '',//成交信息来源
        tenantSource: '',//成交信息来源
        estCompany: '',//地产公司
        estSalesman: '',//地产销售
        estSlaesmanMobile: '',//地产销售手机号
        remark: '',//备注
        agreementStateId: '',//协议状态
        agreementState: '',//协议状态
        abortReason: '',//终止原因
        elePricePerDegree: '',//电费标准
        waterPricePerTon: '',//水费标准
        airConditionFeesMethodId: '',//空调计费方式
        airConditionFeesMethod: '',//空调计费方式
        airConditionFeesUnitPrice: '',//计费单价
        incrDetailsList: [],//递增周期
        deleteIncrDetailsList: [],//删除的递增周期
        contractStartToEnd: [],//合同租期
        agreementStartToEnd: [],//协议租期
        freeStartToEnd: [],//免租期
        rEndTime: "",
      },
      dataRules: {
        buildingAbbreviation: [
          {required: true, message: '楼宇简称不能为空', trigger: 'change'}
        ],
        agreementNumberDate: [
          {required: true, message: '日期不能为空', trigger: 'change'}
        ],
        agreementNumberIndex: [
          {required: true, message: '编号不能为空', trigger: 'change'}
        ],
        zoneId: [
          {required: true, message: '区域不能为空', trigger: 'change'}
        ],
        buildingId: [
          {required: true, message: '楼宇不能为空', trigger: 'blur'},
          {required: true, message: '楼宇不能为空', trigger: 'change'}
        ],
        /*buildingProvinceAndCity: [
          {required: true, message: '楼宇地址不能为空', trigger: 'blur'},
          {required: true, message: '楼宇地址不能为空', trigger: 'change'}
        ],
        buildingAddress: [
          {required: true, message: '楼宇详细地址不能为空', trigger: 'blur'},
          {required: true, message: '楼宇详细地址不能为空', trigger: 'change'}
        ],*/
        roomIdList: [
          {required: true, message: '房号不能为空', trigger: 'blur'},
          {required: true, message: '房号不能为空', trigger: 'change'}
        ],
        meteBase: [
          {required: true, message: '仪表底数不能为空', trigger: 'blur'},
          {required: true, message: '仪表底数不能为空', trigger: 'change'}
        ],
        tenantId: [
          {required: true, message: '承租方(乙方)不能为空', trigger: 'blur'},
          {required: true, message: '承租方(乙方)不能为空', trigger: 'change'}
        ],
        partyAId: [
          {required: true, message: '出租方(甲方)不能为空', trigger: 'blur'},
          {required: true, message: '出租方(甲方)不能为空', trigger: 'change'}
        ],
        /*tenantProvinceAndCity: [
          {required: true, message: '乙方地址不能为空', trigger: 'blur'},
          {required: true, message: '乙方地址不能为空', trigger: 'change'}
        ],
        aProvinceAndCity: [
          {required: true, message: '甲方地址不能为空', trigger: 'blur'},
          {required: true, message: '甲方地址不能为空', trigger: 'change'}
        ],
        tenantAddress: [
          {required: true, message: '承租方(乙方)详细地址不能为空', trigger: 'blur'},
          {required: true, message: '承租方(乙方)详细地址不能为空', trigger: 'change'}
        ],
        aAddress: [
          {required: true, message: '承租方(甲方)详细地址不能为空', trigger: 'blur'},
          {required: true, message: '承租方(甲方)详细地址不能为空', trigger: 'change'}
        ],*/
        contractor: [
          {required: true, message: '乙方联系人不能为空', trigger: 'blur'},
          {required: true, message: '乙方联系人不能为空', trigger: 'change'}
        ],
        contractorMobile: [
          {required: true, message: '乙方联系人联系方式不能为空', trigger: 'blur'},
          {required: true, message: '乙方联系人联系方式不能为空', trigger: 'change'}
        ],
        aLinkman: [
          {required: true, message: '甲方联系人不能为空', trigger: 'blur'},
          {required: true, message: '甲方联系人不能为空', trigger: 'change'}
        ],
        aContact: [
          {required: true, message: '甲方联系方式不能为空', trigger: 'blur'},
          {required: true, message: '甲方联系方式不能为空', trigger: 'change'}
        ],
        tenantOrgCode: [
          {required: true, message: '乙方身份证/组织机构代码证不能为空', trigger: 'blur'},
          {required: true, message: '乙方身份证/组织机构代码证不能为空', trigger: 'change'}
        ],
        aOrgCode: [
          {required: true, message: '甲方组织机构代码证不能为空', trigger: 'blur'},
          {required: true, message: '甲方组织机构代码证不能为空', trigger: 'change'}
        ],
        corpRepresentative: [
          {required: false, message: '乙方公司法人不能为空', trigger: 'blur'}
        ],
        managementTypeId: [
          {required: true, message: '乙方经营类型不能为空', trigger: 'blur'},
          {required: true, message: '乙方经营类型不能为空', trigger: 'change'}
        ],
        id: [
          {required: true, message: '收款账户不能为空', trigger: 'blur'},
          {required: true, message: '收款账户不能为空', trigger: 'change'}
        ],
        accountBank: [
          {required: true, message: '收款账户不能为空', trigger: 'blur'},
          {required: true, message: '收款账户不能为空', trigger: 'change'}
        ],
        accountNumber: [
          {required: true, message: '收款账户不能为空', trigger: 'blur'},
          {required: true, message: '收款账户不能为空', trigger: 'change'}
        ],
        contractDate: [
          {required: true, message: '签约日期不能为空', trigger: 'blur'}
        ],
        leasePaymentDate: [
          {required: true, message: '约定交租日期不能为空', trigger: 'blur'},
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
        agreementStartToEnd: [
          {required: true, message: '协议租期不能为空', trigger: 'blur'}
        ],
        rentArea: [
          {required: true, message: '出租面积不能为空', trigger: 'blur'}
        ],
        regArea: [
          {required: true, message: '注册面积不能为空', trigger: 'blur'}
        ],
        leaseTypeId: [
          {required: true, message: '租赁类型不能为空', trigger: 'change'}
        ],
        agreementTypeId: [
          {required: true, message: '协议类型不能为空', trigger: 'change'}
        ],
        elePricePerDegree: [
          {required: true, message: '电费标准不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("电费标准不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "change"
          },
          {
            validator: (rule, value, callback) => {
              if (value < 0) {
                callback(new Error("电费标准不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          }
        ],
        airConditionFeesMethodId: [
          {required: true, message: '空调计费方式不能为空', trigger: 'blur'},
          {required: true, message: '空调计费方式不能为空', trigger: 'change'}
        ],
        airConditionFeesUnitPrice: [
          {required: false, message: '计费单价不能为空', trigger: 'blur'},
          {
            required: false,
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("计费单价不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        rentFeeUnit: [
          {required: true, message: '租金单价不能为空', trigger: 'blur'},
          {
            validator: (rule, value, callback) => {
              if (value <= 0) {
                callback(new Error("租金单价不能小于等于0"));
              } else {
                callback();
              }
            }, trigger: "blur"
          },
        ],
        monthlyRentFee: [
          {required: true, message: '月租金不能为空', trigger: 'blur'},
          {required: true, message: '月租金不能为空', trigger: 'change'},
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
        rentCollectionMethodId: [
          {required: true, message: '租金收取方式不能为空', trigger: 'change'}
        ],
        depositsTypeId: [
          {required: true, message: '房屋押金类型不能为空', trigger: 'change'}
        ],
        deposits: [
          {required: true, message: '房屋押金不能为空', trigger: 'change'}
        ],
        freePeriod: [
          {required: true, message: '有无免租期不能为空', trigger: 'change'}
        ],
        freeStartToEnd: [
          {required: false, message: '免租期不能为空', trigger: 'blur'}
        ],
        incrementalState: [
          {required: true, message: '有无递增周期不能为空', trigger: 'change'}
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
        decorationStatus: [
          {required: true, message: '是否带装修不能为空', trigger: 'blur'}
        ],
        aid: [
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
        managementIncluded: [
          {required: true, message: '是否包含管理费不能为空', trigger: 'blur'}
        ],
        managementFees: [
          {required: false, message: '管理费不能为空', trigger: 'blur'}
        ],
        taxIncluded: [
          {required: true, message: '是否包含税不能为空', trigger: 'blur'}
        ],
        taxesFees: [
          {required: false, message: '税费不能为空', trigger: 'blur'}
        ],
        tenantSourceId: [
          {required: true, message: '信息来源不能为空', trigger: 'change'}
        ],
      },
    }
  },
  computed: {
    configs() {
      return this.$store.getters["sys/configs"];
    }
  },
  async mounted() {
    await this.loadTenants();//乙方列表
    await this.loadCompany();//经营公司
    await this.loadAccount();//甲方账户
    //this.loadBuildings();
    //this.loadRooms();
    if (this.renewNew) {
      this.$store.dispatch("breadcrumb/addPath", {
        title: "续签租户协议",
        action: "renewHtTenantAgreement"
      });
      this.loadRenewData();
    }else if (this.addNew) {
      this.$store.dispatch('breadcrumb/addPath', {
        title: '新增租户协议',
        action: 'addHtTenantAgreement'
      });
    } else {
      this.$store.dispatch('breadcrumb/addPath', {
        title: '编辑租户协议',
        action: 'editHtTenantAgreement'
      });
      this.loadData();
    }

  },
  methods: {
    // 时间选择器
    // 开始时间 incrPeriodStartAt
    // 结束时间 incrPeriodEndAt
    changeDate(index) {
      if (this.dataForm.incrDetailsList != null) {
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
      }
    },
    /**
     * 递增金额计算
     * incrRatio 递增比例
     * incrUnitPrice 递增单价=原租金单价*递增比例
     * rentFeeUnit 原租金单价=递增单价*面积
     * incrFee 递增金额=递增后单价*面积
     * incrUnitPrice 租金单价
     * rentArea 出租面积
     * afterIncrUnitPrice 递增后单价=原租金单价+递增单价
     * @param index
     */
    changeInfo(index) {
      if (index === 0) {
        let incrRatio = this.dataForm.incrDetailsList[index].incrRatio;
        this.dataForm.incrDetailsList[index].incrUnitPrice = Math.floor(this.dataForm.rentFeeUnit * (incrRatio * 0.01) * 100) / 100
        this.dataForm.incrDetailsList[index].incrFee = +this.dataForm.incrDetailsList[index].incrUnitPrice * this.dataForm.rentArea
        this.dataForm.incrDetailsList[index].afterIncrUnitPrice = +this.dataForm.rentFeeUnit + Number(this.dataForm.incrDetailsList[index].incrUnitPrice) 
        this.dataForm.incrDetailsList[index].afterIncrFee = Math.floor(this.dataForm.incrDetailsList[index].afterIncrUnitPrice * this.dataForm.rentArea * 100) / 100 
      } else {
        let incrRatio = this.dataForm.incrDetailsList[index].incrRatio;
        this.dataForm.incrDetailsList[index].incrUnitPrice = Math.floor(+this.dataForm.incrDetailsList[index - 1].afterIncrUnitPrice * (incrRatio * 0.01) * 100) / 100 
        this.dataForm.incrDetailsList[index].incrFee = +this.dataForm.incrDetailsList[index].incrUnitPrice * this.dataForm.rentArea 
        this.dataForm.incrDetailsList[index].afterIncrUnitPrice = +this.dataForm.rentFeeUnit + Number(this.dataForm.incrDetailsList[index].incrUnitPrice)  
        this.dataForm.incrDetailsList[index].afterIncrFee = Math.floor(this.dataForm.incrDetailsList[index].afterIncrUnitPrice * this.dataForm.rentArea * 100) / 100 
      }
    },
    setFormDate(){
        const length = this.dataForm.incrDetailsList.length
        for (let index = 0; index < length; index++) {
          this.changeInfo(index)
        }
    },
    /**
     * 加载编辑数据
     * @returns {Promise<void>}
     */
    async loadData() {
      const result = await api.htTenantAgreement.getById(this.$route.params.id);
      if (result.code === 200) {
        await this.loadBuildings(result.data.zoneId);
        await this.loadRooms({
          buildingId: result.data.buildingId
        });
        this.dataForm = result.data;
        this.dataForm.partyAId = Number(this.dataForm.partyAId);
        const splitAgreementNumber = this.dataForm.agreementNumber.split("-");
        this.dataForm.buildingAbbreviation = splitAgreementNumber[0];
        this.$set(this.dataForm, "agreementNumberDate", splitAgreementNumber[1])
        this.$set(this.dataForm, "agreementNumberIndex", splitAgreementNumber[2])

        this.dataForm.meters = []
        if (this.dataForm.renew === 1) {
          //查询房间仪表列表
          for (const item of this.dataForm.roomIdList) {
            const result = await api.htTenantAgreement.listMeter(item);
            if (result.code === 200) {
              if (result.data != null && result.data.length !== 0) {
                result.data.forEach(me => {
                  const m =  this.dataForm.meterList.find(it => {
                    return Number(it.meterId) === me.id;
                  })
                  console.log(!(!m || m.meteBase === 0))
                  const a = {
                    roomId: item,
                    roomNum: me.roomNum,
                    meterId: me.id,
                    meterNumber: me.meterNumber,
                    meterName: me.meterName,
                    meterTypeId: me.meterTypeId,
                    meterType: me.meterType,
                    meteBase: !!m ? m.meteBase : "",
                    inputDisabled : !(!m || m.meteBase !== 0),
                    switch: !!m,
                  }
                  this.dataForm.meters.push(a);
                })
              }
            }
          }
        }else {
          this.dataForm.meterList.forEach(meter => {
            this.$set(meter, "switch", true)
            this.dataForm.meters.push(meter)
          })
        }
        this.selectRoomId = this.dataForm.roomIdList;
        if (this.dataForm.tenantProvince != null && this.dataForm.tenantProvince.trim().length != 0 && this.dataForm.tenantCity != null && this.dataForm.tenantCity.trim().length != 0) {
          this.dataForm.tenantProvinceAndCity = [TextToCode[this.dataForm.tenantProvince].code, TextToCode[this.dataForm.tenantProvince][this.dataForm.tenantCity].code];
        }
        if (this.dataForm.aProvince != null && this.dataForm.aProvince.trim().length != 0 && this.dataForm.aCity != null && this.dataForm.aCity.trim().length != 0) {
          this.dataForm.aProvinceAndCity = [TextToCode[this.dataForm.aProvince].code, TextToCode[this.dataForm.aProvince][this.dataForm.aCity].code];
        }
        if (this.dataForm.buildingProvince != null && this.dataForm.buildingProvince.trim().length != 0 && this.dataForm.buildingCity != null && this.dataForm.buildingCity.trim().length != 0) {
          this.dataForm.buildingProvinceAndCity = [TextToCode[this.dataForm.buildingProvince].code, TextToCode[this.dataForm.buildingProvince][this.dataForm.buildingCity].code];
        }
        const tenant = this.tenants.find(item => {
          return item.id === this.dataForm.tenantId
        })
        this.dataForm.tenantTypeId = tenant.tenantTypeId;
        this.dataForm.freePeriod = this.dataForm.freePeriod != null ? this.dataForm.freePeriod : 0;
        this.dataForm.incrementalState = this.dataForm.incrementalState != null ? this.dataForm.incrementalState : 0;
        if (this.dataForm.contractStartAt != null && this.dataForm.contractEndAt != null) {
          this.$set(this.dataForm, 'contractStartToEnd', [this.dataForm.contractStartAt, this.dataForm.contractEndAt])
        }else {
          this.$set(this.dataForm, 'contractStartToEnd', [])
        }
        this.$set(this.dataForm, 'agreementStartToEnd', [this.dataForm.agreementStartAt, this.dataForm.agreementEndAt])
        if (this.dataForm.freePeriod === 1) {
          this.$set(this.dataForm, 'freeStartToEnd', [this.dataForm.freeStartAt, this.dataForm.freeEndAt])
          this.dataRules.freeStartToEnd[0].required = true;
        } else {
          this.$set(this.dataForm, 'freeStartToEnd', [])
          this.dataRules.freeStartToEnd[0].required = false;
        }
        if (this.dataForm.incrDetailsList === null) {
          this.dataForm.incrDetailsList = []
        }
      } else {
        this.$message.error(result.msg);
      }
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
    },

    /**
     * 加载续签数据
     * @returns {Promise<void>}
     */
    async loadRenewData() {
      const result = await api.htTenantAgreement.getById(this.$route.params.rid);
      if (result.code === 200) {
        const reData = result.data;
        await this.loadBuildings(reData.zoneId);
        await this.loadRooms({
          buildingId: reData.buildingId
        });
        const splitAgreementNumber = reData.agreementNumber.split("-");
        this.dataForm.buildingAbbreviation = splitAgreementNumber[0];
        this.dataForm.rid = this.$route.params.rid;
        this.dataForm.zoneId = reData.zoneId;
        this.dataForm.zoneName = reData.zoneName;
        this.dataForm.buildingId = reData.buildingId;
        this.dataForm.buildingName = reData.buildingName;
        if (reData.buildingProvince != null && reData.buildingProvince.trim().length != 0 && reData.buildingCity != null && reData.buildingCity.trim().length != 0) {
          this.dataForm.buildingProvinceAndCity = [TextToCode[reData.buildingProvince].code, TextToCode[reData.buildingProvince][reData.buildingCity].code];
        }
        this.dataForm.buildingAddress = reData.buildingAddress;
        this.dataForm.roomIdList = reData.roomIdList;
        this.dataForm.agreementStartToEndDefaultValue = new Date(reData.agreementEndAt) + 8.64e7;
        this.dataForm.agreementStartToEndpickerOptions = {
          disabledDate(time) {
            return time.getTime() < new Date(reData.agreementEndAt)
          }
        }

        //查询房间仪表列表
        const roomMeters = [];
        for (const item of this.dataForm.roomIdList) {
          const result = await api.htTenantAgreement.listMeter(item);
          if (result.code === 200) {
            if (result.data != null && result.data.length !== 0) {
              result.data.forEach(me => {
                const m = reData.meterList.find(it => {
                  return Number(it.meterId) === me.id;
                })
                const a = {
                  roomId: item,
                  roomNum: me.roomNum,
                  meterId: me.id,
                  meterNumber: me.meterNumber,
                  meterName: me.meterName,
                  meterTypeId: me.meterTypeId,
                  meterType: me.meterType,
                  meteBase: !!m ? "0" : "",
                  inputDisabled : !!m,
                  switch: !!m,
                }
                roomMeters.push(a);
              })
            }
          }
        }
        this.dataForm.meters = roomMeters
        this.dataForm.partyAId = Number(reData.partyAId);
        if (reData.aProvince != null && reData.aProvince.trim().length != 0 && reData.aCity != null && reData.aCity.trim().length != 0) {
          this.dataForm.aProvinceAndCity = [TextToCode[reData.aProvince].code, TextToCode[reData.aProvince][reData.aCity].code];
        }
        this.dataForm.aAddress = reData.aAddress;
        this.dataForm.aLinkman = reData.aLinkman
        this.dataForm.aContact = reData.aContact;
        this.dataForm.aOrgCode = reData.aOrgCode;

        this.dataForm.tenantId = reData.tenantId;
        if (reData.tenantProvince != null && reData.tenantProvince.trim().length != 0 && reData.tenantCity != null && reData.tenantCity.trim().length != 0) {
          this.dataForm.tenantProvinceAndCity = [TextToCode[reData.tenantProvince].code, TextToCode[reData.tenantProvince][reData.tenantCity].code];
        }
        this.dataForm.tenantAddress = reData.tenantAddress;
        this.dataForm.contractor = reData.contractor;
        this.dataForm.contractorMobile = reData.contractorMobile;
        this.dataForm.tenantOrgCode = reData.tenantOrgCode;

        this.dataForm.tenantTypeId = reData.tenantTypeId;
        this.dataForm.corpRepresentative = reData.corpRepresentative;
        this.dataForm.managementTypeId = reData.managementTypeId;

        this.dataForm.accountList = reData.accountList;
        this.dataForm.tenantSourceId = reData.tenantSourceId;

        this.dataForm.rentArea = reData.rentArea;
        this.dataForm.regArea = reData.regArea;
        this.dataForm.leaseTypeId = reData.leaseTypeId;
        this.dataForm.agreementTypeId = reData.agreementTypeId;
        this.dataForm.waterPricePerTon = reData.waterPricePerTon;
        this.dataForm.elePricePerDegree = reData.elePricePerDegree;
        this.$set(this.dataForm, 'airConditionFeesMethodId', reData.airConditionFeesMethodId);
        this.dataForm.airConditionFeesMethod = dict.description(this.configs.airConditionFeesType, reData.airConditionFeesMethodId);
        if (reData.airConditionFeesMethodId === 2 || reData.airConditionFeesMethodId ===3) {
          this.dataRules.airConditionFeesUnitPrice[0].required = true;
          this.dataRules.airConditionFeesUnitPrice[1].required = true;
        }
        this.dataForm.airConditionFeesUnitPrice = reData.airConditionFeesUnitPrice;

        this.dataForm.rentFeeUnit = reData.rentFeeUnit;
        this.dataForm.monthlyRentFee = reData.monthlyRentFee;
        this.dataForm.rentCollectionMethodId  = reData.rentCollectionMethodId;
        this.dataForm.depositsTypeId = reData.depositsTypeId;
        this.dataForm.deposits = reData.deposits;
      }
    },

    /**
     * 保存数据
     * @returns {Promise<void>}
     */
    async save() {
      this.$refs.dataForm.validate(async (valid) => {
        if (valid) {
          let flag = true;
          if (this.dataForm.contractStartToEnd[1] > this.dataForm.agreementStartToEnd[1]) {
            await this.$confirm("备案租期到期时间大于协议租期到期时间,是否确认保存?", {
              confirmButtonText: '确认',
              cancelButtonText: '取消'
            })
              .then(() => {
                flag = true
              })
              .catch(() => {
                flag = false
              })
          }
          if (flag) {
            this.sending = true;
            this.dataForm.meterList = this.dataForm.meters;
            this.dataForm.meterList = this.dataForm.meterList.filter(item => item.switch)
            this.dataForm.agreementNumber = this.dataForm.buildingAbbreviation + "-" + this.dataForm.agreementNumberDate
              + "-" + this.formatZero(this.dataForm.agreementNumberIndex, 3)
            this.dataForm.managementType = dict.description(this.configs.managementTypes, this.dataForm.managementTypeId);
            this.dataForm.agreementType = dict.description(this.configs.agreementTypes, this.dataForm.agreementTypeId);
            this.dataForm.tenantSource = dict.description(this.configs.sourceTypes, this.dataForm.tenantSourceId);
            this.dataForm.rentCollectionMethod = dict.description(this.configs.rentCollectionMethods, this.dataForm.rentCollectionMethodId)
            this.dataForm.depositsType = dict.description(this.configs.depositMultiple, this.dataForm.depositsTypeId)
            this.dataForm.leaseType = dict.description(this.configs.leaseTypes, this.dataForm.leaseTypeId);
            this.dataForm.zoneName = dict.description(this.configs.zones, this.dataForm.zoneId);
            this.dataForm.contractStartAt = this.dataForm.contractStartToEnd[0];
            this.dataForm.contractEndAt = this.dataForm.contractStartToEnd[1];
            this.dataForm.agreementStartAt = this.dataForm.agreementStartToEnd[0];
            this.dataForm.agreementEndAt = this.dataForm.agreementStartToEnd[1];
            if (this.dataForm.freePeriod === 1) {
              this.dataForm.freeStartAt = this.dataForm.freeStartToEnd[0];
              this.dataForm.freeEndAt = this.dataForm.freeStartToEnd[1];
            } else {
              this.dataForm.freeStartAt = "";
              this.dataForm.freeEndAt = "";
            }
            this.dataForm.tenantProvince = CodeToText[this.dataForm.tenantProvinceAndCity[0]];
            this.dataForm.tenantCity = CodeToText[this.dataForm.tenantProvinceAndCity[1]];
            this.dataForm.aProvince = CodeToText[this.dataForm.aProvinceAndCity[0]];
            this.dataForm.aCity = CodeToText[this.dataForm.aProvinceAndCity[1]];
            this.dataForm.buildingProvince = CodeToText[this.dataForm.buildingProvinceAndCity[0]];
            this.dataForm.buildingCity = CodeToText[this.dataForm.buildingProvinceAndCity[1]];
            if (this.dataForm.incrementalState === 0) {
              if (this.dataForm.deleteIncrDetailsList === undefined || this.dataForm.deleteIncrDetailsList === null) {
                this.dataForm.deleteIncrDetailsList = []
              }
              if (this.dataForm.incrDetailsList != null && this.dataForm.incrDetailsList.length > 0) {
                this.dataForm.incrDetailsList.forEach(incr => {
                  if (incr.id != null) {
                    this.dataForm.deleteIncrDetailsList.push(incr)
                  }
                })
              }
              this.dataForm.incrDetailsList = []
            }
            if (this.dataForm.otherDepositList.length > 0) {
              this.dataForm.otherDepositList.forEach(otherDeposit => {
                otherDeposit.name = dict.description(
                  this.configs.swDepositTypes,
                  otherDeposit.id
                );
              })
            }
            let params = Object.assign({}, this.dataForm);
            if (this.fileIds.length) {
              params.fileIds = this.fileIds
            }
            //只有当fileStatus为1时才能保存
            if (this.fileStatus === 1) {
              this.sending = false;
              const result = await api.htTenantAgreement[this.renewNew ? "renew" : (this.$route.params.id ? 'updateById' : 'add')](params);
              const tip = this.renewNew ? '续签成功' : (this.$route.params.id ? '更新成功' : '新增成功');
              if (result.code === 200) {
                this.$message.success(tip);
                this.$router.go(-1);
              } else {
                this.$message.error(result.msg);
              }
            } else {
              this.sending = false;
              this.$message.warning("请上传已选择的文件！");
            }
          }
        } else {
          this.$message.error("请检查必填项是否全部填写");
          return false;
        }
      });
      return
    },

    /**
     * 文件相关
     * @param file
     * @returns {boolean}
     */
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

    /**
     * 文件相关
     * @param res
     * @param file
     */
    handleAvatarSuccess(res, file) {
      if (res.code === 200) {
        this.$message.success('上传成功!');
        this.fileIds.push(res.data.fileId);
        this.fileStatus = 1;
      }
    },

    /**
     * 文件相关
     */
    submitUpload() {
      this.$refs.upload.submit();
    },

    /**
     * 查看文件
     * @param row
     */
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
     * 乙方信息
     * @returns {Promise<void>}
     */
    async loadTenants() {
      const result = await api.htTenantAgreement.listTenants();
      result.data.forEach(tenant => {
        tenant.value = tenant.tenantName
        this.tenants.push(tenant)
      })
    },

    /**
     * 选择乙方后
     * @param id
     */
    selectTenant(id) {
      if (id) {
        const obj = this.tenants.find(item => {
          return item.id === id;
        });
        this.dataForm.tenantId = obj.id;
        this.dataForm.tenantName = obj.tenantName;
        this.dataForm.tenantTypeId = obj.tenantTypeId;
        this.dataForm.corpRepresentative = obj.corpRepresentative;
        this.dataForm.managementTypeId = obj.managementTypeId;
        this.dataForm.contractor = obj.linkman;
        this.dataForm.contractorMobile = obj.contactMobile;
        this.dataForm.tenantSourceId = obj.tenantSourceId;
        this.dataForm.tenantOrgCode = obj.tenantCert
        if (obj.province != null && obj.province.trim().length != 0 && obj.city != null && obj.city.trim().length != 0) {
          this.dataForm.tenantProvinceAndCity = [TextToCode[obj.province].code, TextToCode[obj.province][obj.city].code];
        }
        this.dataForm.tenantAddress = obj.address
        //个人时公司法人不显示,非必填
        if (obj.tenantTypeId === 1) {
          this.dataRules.corpRepresentative[0].required = true;
          //this.corpRepresentativeDisabled = true;
        } else {
          this.dataRules.corpRepresentative[0].required = false;
          //this.corpRepresentativeDisabled = false;
        }
      } else {
        this.dataForm.tenantId = "";
        this.dataForm.tenantName = "";
        this.dataForm.tenantTypeId = "";
        this.dataForm.corpRepresentative = "";
        this.dataForm.managementTypeId = "";
        this.dataForm.contractor = "";
        this.dataForm.contractorMobile = "";
        this.dataForm.tenantSourceId = "";
        this.dataForm.tenantOrgCode = "";
        this.dataForm.tenantProvinceAndCity = [];
        this.dataForm.tenantAddress = "";
        this.dataRules.corpRepresentative[0].required = false;
      }
    },

    /**
     * 甲方收款账户
     * @returns {Promise<void>}
     */
    async loadAccount() {
      const params = {
        page: 1,
        pageSize: 1000,
      };
      const result = await api.cwAccount.listPage(params);
      if (result.code === 200) {
        this.accounts = result.data.records;
      }
    },

    /**
     * 添加收款账户
     */
    addAccount() {
      if (this.dataForm.accountList === undefined || this.dataForm.accountList === null) {
        this.dataForm.accountList = [];
      }
      this.dataForm.accountList.push({
        id: "",
        incomeMethodId: "",
        incomeMethod: "",
        accountName: "",
        accountBank: "",
        accountNumber: "",
      });
    },

    /**
     * 选择甲方收款账户
     * @param index
     * @param rows
     */
    selectAccount(index, rows) {
      if (rows[index].id) {
        if (this.accounts) {
          const obj = this.accounts.find(item => {
            return item.id === rows[index].id;
          });
          if (!obj) {
            return;
          }
          rows[index].incomeMethodId = obj.incomeMethodId;
          rows[index].incomeMethod = obj.incomeMethod;
          rows[index].accountName = obj.accountName;
          rows[index].accountBank = obj.accountBank;
          rows[index].accountNumber = obj.accountNumber;
        }
      } else {
        rows[index].incomeMethodId = "";
        rows[index].incomeMethod = "";
        rows[index].accountName = "";
        rows[index].accountBank = "";
        rows[index].accountNumber = "";
      }
    },

    /**
     * 删除甲方收款账户
     * @param index
     * @param rows
     */
    deleteAccount(index, rows) {
      rows.splice(index, 1);
    },

    /**
     * 甲方经营公司
     * @returns {Promise<void>}
     */
    async loadCompany() {
      const result = await api.htOwnerContract.getCompany();
      if (result.code === 200) {
        this.companyList = result.data;
      } else {
        this.$message.error(result.msg);
      }
    },

    /**
     * 选择甲方经营公司
     * @param id
     */
    selectCompany(id) {
      if (id) {
        if (this.companyList) {
          const obj = this.companyList.find(item => {
            return item.id === id;
          });
          if (!obj) {
            return;
          }
          this.dataForm.partyA = obj.companyName;
          if (obj.province != null && obj.province.trim().length != 0 && obj.city != null && obj.city.trim().length != 0) {
            this.dataForm.aProvinceAndCity = [TextToCode[obj.province].code, TextToCode[obj.province][obj.city].code];
          }
          this.dataForm.aAddress = obj.detailedAddress;
          this.dataForm.aLinkman = obj.linkman;
          this.dataForm.aContact = obj.linkPhone;
          this.dataForm.aOrgCode = obj.organizationCode;
        }
      } else {
        this.dataForm.partyA = "";
        this.dataForm.aProvinceAndCity = [];
        this.dataForm.aAddress = "";
        this.dataForm.aLinkman = "";
        this.dataForm.aContact = "";
        this.dataForm.aOrgCode = "";
      }
    },

    /**
     * 选择区域
     * @param id
     */
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

    /**
     * 楼宇
     * @param zoneId
     * @returns {Promise<void>}
     */
    async loadBuildings(zoneId) {
      this.buildings = [];
      if (zoneId) {
        const params = Object.assign(
          {zoneId: zoneId},
        );
        const result = await api.building.getBuildingByZone(params);
        if (result.data != null && result.data.length != 0) {
          let buildings = []
          result.data.forEach(building => {
            building.value = building.building
            buildings.push(building)
          })
          this.buildings = buildings;
        } else {
          this.buildings = [];
        }
      }
    },

    /**
     * 选择楼宇
     * @param id
     */
    selectBuilding(id) {
      if (id) {
        const obj = this.buildings.find(item => {
          return item.buildingId === id;
        });
        this.dataForm.buildingName = obj.building;
        this.dataForm.buildingAddress = obj.address;
        this.dataForm.buildingAbbreviation = obj.buildingAbbreviation;
        if (obj.province != null && obj.province.trim().length != 0 && obj.city != null && obj.city.trim().length != 0) {
          this.dataForm.buildingProvinceAndCity = [TextToCode[obj.province].code, TextToCode[obj.province][obj.city].code];
        }
        this.loadRooms({
          roomStateId: 2,
          enable: 0,
          buildingId: id
        });
      } else {
        this.dataForm.buildingId = "";
        this.dataForm.buildingAddress = "";
        this.dataForm.buildingProvinceAndCity = [];
        this.rooms = []
      }
      this.dataForm.roomNumList = [];
      this.dataForm.roomIdList = [];
      this.dataForm.meters = [];
      this.selectRoomId = [];
    },

    /**
     * 房间
     * @param params
     * @returns {Promise<void>}
     */
    async loadRooms(params) {
      const result = await api.htTenantAgreement.listRooms(params);
      this.rooms = result.data;
    },

    /**
     * 选择房间
     * @param ids
     */
    selectRoom(ids) {
      if (this.selectRoomId === undefined || this.selectRoomId === null) {
        this.selectRoomId = [];
      }
      if (this.dataForm.roomNumList === undefined || this.dataForm.roomNumList === null) {
        this.dataForm.roomNumList = [];
      }
      ids.forEach(id => {
        if (this.selectRoomId.indexOf(id) === -1) {
          this.selectRoomId.push(id);
          const obj = this.rooms.find(item => {
            return item.id === id;
          })
          this.dataForm.roomNumList.push(obj.roomNum);
          this.dataForm.regArea += obj.regArea;
          this.dataForm.rentArea += obj.rentArea;
          this.dataForm.elePricePerDegree = obj.elePricePerDegree;
          this.addMeter(id);
        }
      })
      this.selectRoomId.forEach(rid => {
        if (ids.indexOf(rid) === -1) {
          this.selectRoomId.remove(rid)
          const obj = this.rooms.find(item => {
            return item.id === rid;
          })
          this.dataForm.roomNumList.remove(obj.roomNum);
          this.dataForm.regArea -= obj.regArea;
          this.dataForm.rentArea -= obj.rentArea;
          //this.dataForm.monthlyRentFee -= obj.monthlyRentFee;
          this.removeMeter(rid);
        }
      })
    },

    /**
     * 增加一个房间的仪表
     * @param id
     * @returns {Promise<void>}
     */
    async addMeter(id) {
      const result = await api.htTenantAgreement.listMeter(id);
      if (result.code === 200) {
        if (result.data != null && result.data.length !== 0) {
          result.data.forEach(meter => {
            this.dataForm.meters.push({
              roomId: id,
              roomNum: meter.roomNum,
              meterId: meter.id,
              meterNumber: meter.meterNumber,
              meterName: meter.meterName,
              meterTypeId: meter.meterTypeId,
              meterType: meter.meterType,
              meteBase: "",
              switch: true,
            })
          })
        }
      }
    },

    /**
     * 移除一个房间的仪表
     * @param id
     */
    removeMeter(id) {
      for (let i = this.dataForm.meters.length - 1; i > -1; i--) {
        if (Number(this.dataForm.meters[i].roomId) === id) {
          this.dataForm.meters.remove(this.dataForm.meters[i]);
        }
      }
    },

    /**
     * 免租期
     * @param value
     */
    cleanFreePeriod(value) {
      this.dataRules.freeStartToEnd[0].required = value === 1;
    },

    /**
     * 递增周期增加一行
     */
    addIncrDetails(index) {
      if (this.dataForm.incrDetailsList=== undefined || this.dataForm.incrDetailsList === null) {
        this.dataForm.incrDetailsList = []
      }
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

    /**
     * 递增周期删除一行
     * @param index
     * @param rows
     */
    deleteIncrDetails(index, rows) {
      if (this.dataForm.deleteIncrDetailsList === undefined || this.dataForm.deleteIncrDetailsList === null) {
        this.dataForm.deleteIncrDetailsList = []
      }
      if (rows[index].id != null) {
        this.dataForm.deleteIncrDetailsList.push(rows[index])
      }
      rows.splice(index, 1);
    },

    /**
     * 选择空调计费方式后，增加必填项校验
     * @param id
     */
    cleanAirConditionFees(id) {
      this.dataForm.airConditionFeesMethod = dict.description(this.configs.airConditionFeesType, id);
      if (id !== 1) {
        this.dataRules.airConditionFeesUnitPrice[0].required = true;
        this.dataRules.airConditionFeesUnitPrice[1].required = true;
      }else {
        this.dataRules.airConditionFeesUnitPrice[0].required = false;
        this.dataRules.airConditionFeesUnitPrice[1].required = false;
      }
    },

    /**
     * 计算月租金
     */
    setMonthlyRentFee() {
      if (this.dataForm.rentFeeUnit != null && this.dataForm.rentArea != null) {
        this.dataForm.monthlyRentFee = this.dataForm.rentFeeUnit * this.dataForm.rentArea;
      } else {
        this.dataForm.monthlyRentFee = 0;
      }
      this.setFormDate()
    },

    /**
     * 计算押金
     */
    setDeposits() {
      if (this.dataForm.depositsTypeId != null && this.dataForm.monthlyRentFee != null) {
        this.dataForm.deposits = this.dataForm.monthlyRentFee * this.dataForm.depositsTypeId;
      } else {
        this.dataForm.deposits = 0;
      }
    },

    /**
     * 添加其他押金
     */
    addOtherDeposits() {
      this.dataForm.otherDepositList.push({
        id: "",
        name: "",
        deposit: 0,
      });
    },

    /**
     * 删除其他押金
     * @param index
     * @param rows
     */
    deleteDeposit(index, rows) {
      rows.splice(index, 1);
    },

    /**
     * 管理费
     * @param value
     */
    cleanManagementFees(value) {
      this.dataRules.managementFees[0].required = value !== 1;
    },

    /**
     * 税费
     * @param value
     */
    cleanTaxesFees(value) {
      this.dataRules.taxesFees[0].required = value !== 1;
    },

    //补0
    formatZero(num, len) {
      if (String(num).length > len) return num;
      return (Array(len).join(0) + num).slice(-len);
    },

    //返回
    back() {
      this.$router.go(-1);
    },

  },
};
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {
}

.el-col-1 {
  width: 9px;
}

#SaveCancel {
  margin-top: 60px;
}
</style>
