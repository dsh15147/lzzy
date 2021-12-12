<template>
  <div class="main-content">
    <el-row class="mb-10">
      <el-form :inline="true" size="mini" :model="searchParams"
               ref="searchParams">
        <el-form-item prop="zoneId">
          <el-select clearable filterable placeholder="区域" v-model="searchParams.zoneId">
            <el-option label="全部" value=""></el-option>
            <el-option :key="index" :label="item.name" :value="item.id"
                       v-for="(item, index) in configs.zones"/>
          </el-select>
        </el-form-item>
        <el-form-item prop="buildingName">
          <el-input clearable placeholder="楼宇" size="mini" v-model.trim="searchParams.buildingName"/>
        </el-form-item>
        <el-form-item prop="partyA">
          <el-input clearable placeholder="合同甲方" size="mini" v-model.trim="searchParams.partyA"/>
        </el-form-item>
        <el-form-item prop="rentStartAt">
          <el-date-picker clearable format="yyyy-MM-dd" placeholder="起租日" type="date"
                          v-model="searchParams.rentStartAt" value-format="yyyy-MM-dd"/>
        </el-form-item>
        <el-form-item prop="rentEndAt">
          <el-date-picker clearable format="yyyy-MM-dd" placeholder="到租日" type="date"
                          v-model="searchParams.rentEndAt" value-format="yyyy-MM-dd"/>
        </el-form-item>
        <el-form-item>
          <el-button @click="loadData(1)" size="mini" type="primary">查询</el-button>
          <el-button @click="resetForm('searchParams')" size="mini" type="success">重置</el-button>
          <el-button @click="add" size="mini" type="success" v-if="hasPerms('ownerHT:add')">新增</el-button>
        </el-form-item>
      </el-form>
    </el-row>
    <el-row>
      <el-tabs @tab-click="handleTabClick" class="function-list" type="card"
               v-model="searchParams.contractStateId">
        <el-tab-pane label="所有" name="">
          <el-table :data="data['0']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="合同状态" prop="contractState"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <btn @click="edit(scope.row)" size="mini" type="text"
                           v-show="scope.row.contractStateId===4 || scope.row.contractStateId===6" label="编辑" perms="ownerHT:edit"/>
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
                <btn @click="submitReview(scope.row)" size="mini" type="text"
                           v-show="scope.row.contractStateId===4 || scope.row.contractStateId===6" label="提交审核" perms="ownerHT:submitchk"/>
<!--                <el-button @click="toDialog(scope.row)" size="mini" type="text" v-show="scope.row.contractStateId===5">
                  审核
                </el-button>-->
<!--                <el-button @click="presettlement(scope.row)" size="mini" type="text"
                           v-show="scope.row.contractStateId===1">工程预结算
                </el-button>-->
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>

        <el-tab-pane label="未提交" name="4">
          <el-table :data="data['4']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="edit(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:edit')">编辑</el-button>
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
                <el-button @click="submitReview(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:submitchk')">提交审核</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>
        <el-tab-pane label="待审核" name="5">
          <el-table :data="data['5']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
                <el-button @click="toDialog(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:submitchk')">审核</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>
        <el-tab-pane label="已驳回" name="6">
          <el-table :data="data['6']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="edit(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:edit')">编辑</el-button>
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
                <el-button @click="submitReview(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:submitchk')">提交审核</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>

        <el-tab-pane label="执行中" name="1">
          <el-table :data="data['1']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
                <el-button @click="renew(scope.row)" size="mini" type="text" v-if="scope.row.renewalStatus === 0 && hasPerms('ownerHT:renew')">续签</el-button>
                <el-button @click="presettlement(scope.row)" size="mini" type="text" v-if="scope.row.renew === 0 && hasPerms('ownerHT:settlement')">工程预结算</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>
        <el-tab-pane label="已到期" name="2">
          <el-table :data="data['5']" border class="custom-table" size="mini" v-loading="loading">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>
        <el-tab-pane label="已终止" name="3">
          <el-table :data="data['3']" border class="custom-table" size="mini" v-loading="loading">
            <div class="empty-wrap" slot="empty">
              <i class="iconfont icon-tishi"></i>
              <span>暂无数据</span>
            </div>
            <el-table-column align="center" label="序号" type="index"/>
            <el-table-column align="center" label="合同甲方" prop="partyA" width="220%"/>
            <el-table-column align="center" label="甲方联系人" prop="aLinkman"/>
            <el-table-column align="center" label="合同乙方" prop="partyB" width="220%"/>
            <el-table-column align="left" label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column align="center" label="所属区域" prop="zoneName"/>
            <el-table-column align="center" label="合同编号" prop="contractNum" width="150%"/>
            <el-table-column align="center" label="签约日期" prop="contractDate" width="120%"/>
            <el-table-column align="center" label="承租面积(m²)" prop="rentArea" width="120px"/>
            <el-table-column align="center" :label="'承租单价\n(元/m²)'" prop="rentPricePerSquare" width="90px"/>
            <el-table-column align="center" label="起租日" prop="rentStartAt" width="110%"/>
            <el-table-column align="center" label="到期日" prop="rentEndAt" width="110%"/>
            <el-table-column align="center" label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('ownerHT:detail')">详情</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage" @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10"/>
        </el-tab-pane>
      </el-tabs>
    </el-row>
    <el-dialog :close-on-click-modal="false" class="abort" :visible.sync="showDialog"
               title="业主合同详情" width="1200px">
      <el-form class="data-form" :model="detailObj" ref="dataForm">
        <el-form-item>
          <el-input v-model="detailObj.contractNum" readonly>
            <template slot="prepend">合同编号</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.partyA" readonly>
            <template slot="prepend">合同甲方</template>
          </el-input>
          <el-input v-model="detailObj.aLinkman" readonly>
            <template slot="prepend">甲方联系人</template>
          </el-input>
          <el-input v-model="detailObj.aContact" readonly>
            <template slot="prepend">甲方联系方式</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aProvince + detailObj.aCity + detailObj.aAddress" style="width: 50%;" readonly>
            <template slot="prepend">甲方地址</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aOrgCode" style="width: 66%;" readonly>
            <template slot="prepend">甲方身份证/组织机构代码证</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.partyB" readonly>
            <template slot="prepend">合同乙方</template>
          </el-input>
          <el-input v-model="detailObj.bLinkman" readonly>
            <template slot="prepend">乙方联系人</template>
          </el-input>
          <el-input v-model="detailObj.bContact" readonly>
            <template slot="prepend">乙方联系方式</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.bProvince + detailObj.bCity + detailObj.bAddress" style="width: 50%;" readonly>
            <template slot="prepend">乙方地址</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.bOrgCode" style="width: 66%;" readonly>
            <template slot="prepend">乙方组织机构代码证</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.zoneName" readonly>
            <template slot="prepend">所属区域</template>
          </el-input>
          <el-input v-model="detailObj.buildingName" readonly>
            <template slot="prepend">楼宇</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.buildingProvince + detailObj.buildingCity + detailObj.buildingAddress"
                    style="width: 50%" readonly>
            <template slot="prepend">地址</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.filed?'是':'否'" readonly>
            <template slot="prepend">是否备案</template>
          </el-input>
          <el-input v-model="detailObj.rentFiledNum" readonly>
            <template slot="prepend">租赁备案号</template>
          </el-input>
          <el-input v-model="detailObj.contractDate" readonly>
            <template slot="prepend">签约日期</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="'每月' + detailObj.leasePaymentDate + '日'" readonly>
            <template slot="prepend">约定交租日期</template>
          </el-input>
          <el-input v-model="detailObj.rentArea" readonly>
            <template slot="prepend">承租面积(m²)</template>
          </el-input>
          <el-input v-model="detailObj.rentPricePerSquare" readonly>
            <template slot="prepend">承租单价(元/m²)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.monthlyFee" readonly>
            <template slot="prepend">月租金(元/月)</template>
          </el-input>
          <el-input v-model="detailObj.deposits" readonly>
            <template slot="prepend">房屋押金(元)</template>
          </el-input>
          <el-input v-model="detailObj.tenancyTerm" readonly>
            <template slot="prepend">租期(年)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.rentStartAt + '~' + detailObj.rentEndAt" readonly>
            <template slot="prepend">起租日</template>
          </el-input>
          <el-input v-model="detailObj.freeStartToEnd" readonly>
            <template slot="prepend">免租期</template>
          </el-input>
          <el-input v-model="detailObj.manPricePerSquare" readonly>
            <template slot="prepend">管理费标准(元/m²)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.elePricePerDegree" readonly>
            <template slot="prepend">电费标准(元/度)</template>
          </el-input>
          <el-input v-model="detailObj.waterPricePerTon" readonly>
            <template slot="prepend">水费标准(元/吨)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.electricityMeterReading" readonly>
            <template slot="prepend">电表底数(度)</template>
          </el-input>
          <el-input v-model="detailObj.waterMeterReading" readonly>
            <template slot="prepend">水表底数(吨)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.otherDepositListStr" readonly style="width: 615px">
            <template slot="prepend">其他押金(元)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.ohtersFeeListStr" readonly style="width: 615px">
            <template slot="prepend">其他收费(元)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.managementIncluded?'是':'否'" readonly>
            <template slot="prepend">是否含管理费</template>
          </el-input>
          <el-input v-model="detailObj.taxIncluded?'是':'否'" readonly>
            <template slot="prepend">是否含税</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.incrementalState?'有':'无'" readonly>
            <template slot="prepend">有无递增周期</template>
          </el-input>
        </el-form-item>
        <el-form-item label="递增周期" v-if="detailObj.incrementalState">
          <el-table :data="detailObj.incrDetailsList" style="width: 100%" stripe border>
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
          <el-input type="textarea" v-model="detailObj.specialTerms" readonly/>
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
        <el-form-item label="">
          <el-button type="default" @click="close" size="small">关闭</el-button>
          <el-button type="default" @click="passAudit(detailObj.id)" size="small">审核通过</el-button>
          <el-button type="default" @click="rejectReview(detailObj.id)" size="small">审核驳回</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import api from 'api'
import {API_SERVER} from "../../../config";
import {IMG_SERVER} from "../../../config";
import {hasPermission} from "../../../permission";

export default {
  name: '',
  data() {
    return {
      imgServer: IMG_SERVER,
      apiServer: API_SERVER,
      arr: [],
      files: [],
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
      closeMode: false,
      page: 1,
      pageSize: 10,
      dataList: [],
      totalSize: 10,
      loading: false,
      detailObj: {},
      searchParams: {
        contractStateId: '0',
        buildingId: '',
        buildingName: '',
        ownerId: '',
        contractNum: '',
        partyA: '',
        rentStartAt: '',
        rentEndAt: ''
      },
      data: {
        "0": [],
        "1": [],
        "2": [],
        "3": [],
        "4": [],
        "5": [],
        "6": [],
      },
      showDialog: false,
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
      title: '合同管理/业主合同管理',
      action: 'list-ht-owner-contract'
    });
    if (this.$route.query.buildingName) {
      this.searchParams.buildingName = this.$route.query.buildingName;
    }
    if (this.$route.query.ownerId) {
      this.searchParams.ownerId = this.$route.query.ownerId;
    }
    if (this.$route.query.zoneId) {
      this.searchParams.zoneId = this.$route.query.zoneId;
    }
    if (this.$route.query.buildingId) {
      this.searchParams.buildingId = this.$route.query.buildingId;
    }
    this.loadData();
  },
  methods: {
    hasPerms: function (perms) {
      // 根据权限标识和外部指示状态进行权限判断
      return hasPermission(perms)
    },
    tableRowClassName({row, rowIndex}) {
      return row.warning ? 'warning-row' : "";
    },
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
      const result = await api.htOwnerContract.listPage(params);
      this.loading = false;
      if (result.code === 200) {
        this.$nextTick(() => {
          this.data[this.searchParams.contractStateId] = result.data.records;
          this.totalSize = result.data.total * 1;
        })
      } else {
        this.$message.error(result.msg);
      }
    },
    add() {
      this.$router.push({name: 'addHtOwnerContract'});
    },
    edit(row) {
      this.$router.push({name: 'editHtOwnerContract', params: {id: row.id}});
    },
    renew(row) {
      this.$router.push({name: 'renewHtOwnerContract', params: {rid: row.id}});
    },
    info(row) {
      this.$router.push({name: 'viewHtOwnerContract', params: {id: row.id}});
    },
    submitReview(row) {
      this.$confirm("确定要提交此合同？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htOwnerContract.submitReview(row.id);
          if (res.code == 200) {
            this.$message.success("提交成功");
            this.loadData();
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
    },
    //重置搜索
    resetForm(searchParams) {
      this.$refs[searchParams].resetFields();
      this.loadData(1);
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
    //下载文件
    downloadfiles(row) {
      //let link = `${IMG_SERVER}${this.FileList.fileUrl}`;
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
    async toDialog(row) {
      this.showDialog = true;
      const result = await api.htOwnerContract.getById(row.id)
      if (result.code == 200) {
        this.detailObj = result.data;
        this.detailObj.freeStartToEnd = this.detailObj.freePeriod === 1 ? this.detailObj.freeStartAt + " 至 " + this.detailObj.freeEndAt : "无";
        this.detailObj.ohtersFeeListStr = this.showOtherFee(this.detailObj.otherFeeList)
        this.detailObj.otherDepositListStr = this.showOtherDeposit(this.detailObj.otherDepositList)
        /*if (this.detailObj.incrDetailsList && this.detailObj.incrDetailsList.length > 0) {
          this.detailObj.incrDetailsList.forEach(incr => {
            incr.incrPeriodAt = [
              incr.incrPeriodStartAt,
              incr.incrPeriodEndAt
            ];
          })
        }*/
      }
      if (this.arr.length === 0) {
        api.upload.getFile(row.id, this.FileList.businessCode).then(res => {
          this.files.push(res.data)
          this.files.forEach((item) => {
            for (let i = 0; i < item.length; i++) {
              this.arr.push(item[i]);
            }
          })
        })
      } else {
      }
    },
    close() {
      this.showDialog = false;
      this.detailObj = {};
    },
    passAudit(id) {
      this.$confirm("确定要审核通过此合同？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htOwnerContract.passAudit(id);
          if (res.code == 200) {
            this.$message.success("审核成功");
            this.loadData();
            this.showDialog = false;
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
    },
    rejectReview(id) {
      this.$confirm("确定要驳回此合同？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htOwnerContract.rejectReview(id);
          if (res.code == 200) {
            this.$message.success("驳回成功");
            this.loadData();
            this.showDialog = false;
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
    },
    presettlement(row) {
      this.$router.push({name: 'editHtPreSettlement', params: {id: row.id}});
    },
    del(row) {
      this.$confirm('确定要删除该记录吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        const result = await api.htOwnerContract.delete(row.id);
        if (result.code == 200) {
          this.$message.success('删除成功!');
          this.loadData();
        } else {
          this.$message.error(result.msg);
        }
      }).catch(() => {
      });
    },
    handleTabClick(tab, event) {
      this.loadData(1)
    },
    showOtherFee(fees) {
      if (!fees) {
        return "无";
      }
      if (fees.length > 0) {
        let strList = [];
        fees.forEach(item => {
          let str = item.name + ":" + item.fee + "元";
          strList.push(str);
        })
        return strList.join();
      } else {
        return "无";
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
  }
}
</script>
<style lang="scss" rel="stylesheet/scss">
@import "../../../styles/config";

.main-content {
  .el-table .warning-row {
    color: red;
  }

  .el-table .cell {
    white-space: pre-line;
  }
}
</style>
