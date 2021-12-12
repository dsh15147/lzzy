<template>
  <div class="main-content">
    <el-row class="mb-10">
      <el-form :inline="true" size="mini" :model="searchParams"
               ref="searchParams">
        <el-form-item prop="buildingName">
          <el-input clearable placeholder="楼宇" size="mini" v-model.trim="searchParams.buildingName"></el-input>
        </el-form-item>
        <el-form-item prop="tenantName">
          <el-input clearable placeholder="承租方" size="mini" v-model.trim="searchParams.tenantName"></el-input>
        </el-form-item>

        <el-form-item prop="agreementTypeId">
          <el-select clearable filterable placeholder="协议类型" v-model="searchParams.agreementTypeId">
            <el-option label="全部" value=""></el-option>
            <el-option :key="index" :label="item.name" :value="item.id"
                       v-for="(item, index) in configs.agreementTypes"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="loadData(1)" size="mini" type="primary">查询</el-button>
          <el-button @click="resetForm('searchParams')" size="mini" type="success">重置</el-button>
          <el-button @click="add" size="mini" type="success" v-if="hasPerms('tenantXY:add')">新增</el-button>
        </el-form-item>
      </el-form>
    </el-row>

    <el-row>
      <el-tabs @tab-click="handleTabClick" class="function-list" type="card" v-model="searchParams.agreementStateId">

        <el-tab-pane label="所有" name="">
          <el-table :data="pages['0']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="备案起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="协议状态" prop="agreementState"/>
            <el-table-column label="终止原因" prop="abortReason"/>
            <el-table-column label="操作" prop="" width="200px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="edit(scope.row)" size="mini" type="text"
                           v-if="hasPerms('tenantXY:edit')"
                           v-show="scope.row.agreementStateId===4 || scope.row.agreementStateId===6">编辑
                </el-button>
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')">协议明细</el-button>
                <el-button @click="submitReview(scope.row)" size="mini" type="text"
                           v-if="hasPerms('tenantXY:submitchk')"
                           v-show="scope.row.agreementStateId===4 || scope.row.agreementStateId===6">提交审核
                </el-button>
                <!--                <el-button @click="toDialog(scope.row)" size="mini" type="text" v-if="scope.row.agreementStateId===5">-->
                <!--                  审核-->
                <!--                </el-button>-->
                <el-button @click="abort(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:termination')" v-show="scope.row.agreementStateId===1" >
                  终止
                </el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>

        </el-tab-pane>
        <el-tab-pane label="未提交" name="4">
          <el-table :data="pages['4']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="备案起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="操作" prop="" width="250px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="edit(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:edit')">编辑</el-button>
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')" >协议明细</el-button>
                <el-button @click="submitReview(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:submitchk')">提交审核</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>
        </el-tab-pane>

        <el-tab-pane label="待审核" name="5">
          <el-table :data="pages['5']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="备案起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="操作" prop="" width="250px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')">协议明细</el-button>
                <el-button @click="toDialog(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:submitchk')">审核</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>
        </el-tab-pane>

        <el-tab-pane label="已驳回" name="6">
          <el-table :data="pages['6']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="操作" prop="" width="250px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="edit(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:edit')">编辑</el-button>
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')">协议明细</el-button>
                <el-button @click="submitReview(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:submitchk')">提交审核</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>
        </el-tab-pane>

        <el-tab-pane label="执行中" name="1">
          <el-table :data="pages['1']" border class="custom-table" size="mini" v-loading="loading"
                    :row-class-name="tableRowClassName">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="备案起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="操作" prop="" width="250px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')">协议明细</el-button>
                <el-button @click="abort(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:termination')">终止</el-button>
                <el-button @click="renew(scope.row)" size="mini" type="text" v-if="scope.row.renewalStatus === 0 && hasPerms('tenantXY:renew')">续签</el-button>
                <el-button @click="additions(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:addMsg')">补录备案信息</el-button>
                <!--                <el-button @click="abort(scope.row)" size="mini" type="text">续约</el-button>-->
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>

        </el-tab-pane>

        <el-tab-pane label="已到期" name="2">
          <el-table :data="pages['2']" border class="custom-table" size="mini" v-loading="loading">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="备案起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="操作" prop="" width="250px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')">协议明细</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>

        </el-tab-pane>

        <el-tab-pane label="已终止" name="3">
          <el-table :data="pages['3']" border class="custom-table" size="mini" v-loading="loading">
            <div class="empty-wrap" slot="empty"><i class="iconfont icon-tishi"></i><span>暂无数据</span></div>
            <el-table-column label="序号" type="index"></el-table-column>
            <el-table-column label="区域" prop="zoneName"/>
            <el-table-column label="楼宇" prop="buildingName" width="150%"/>
            <el-table-column label="房号" prop="roomNum"/>
            <el-table-column label="出租面积(m²)" prop="rentArea"/>
            <el-table-column label="承租方" prop="tenantName" width="150%"/>
            <el-table-column label="签约人" prop="contractor"/>
            <el-table-column label="签约人电话" prop="contractorMobile" width="100%"/>
            <el-table-column label="签约日期" prop="contractDate" width="90px"/>
            <el-table-column label="租赁协议编号" prop="agreementNumber" width="200px"/>
            <el-table-column label="协议起租日" prop="agreementStartAt" width="100%"/>
            <el-table-column label="协议到期日" prop="agreementEndAt" width="100%"/>
            <el-table-column label="租赁凭证编号" prop="ticketNumber" width="180px"/>
            <el-table-column label="备案起租日" prop="contractStartAt" width="100%"/>
            <el-table-column label="备案到期日" prop="contractEndAt" width="100%"/>
            <el-table-column label="月租金(元)" align="right" prop="monthlyRentFee"/>
            <el-table-column label="房屋押金(元)" align="right" prop="deposits" width="100px"/>
            <el-table-column label="租赁类型" prop="leaseType" width="110%" align="center"/>
            <el-table-column label="信息来源" prop="tenantSource"/>
            <el-table-column label="备注" prop="remark" width="120%"/>
            <el-table-column label="终止原因" prop="abortReason"/>
            <el-table-column label="操作" prop="" width="250px;" fixed="right">
              <template slot-scope="scope">
                <el-button @click="info(scope.row)" size="mini" type="text" v-if="hasPerms('tenantXY:XYdetail')">协议明细</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination :current-page.sync="page" :page-size="pageSize" :page-sizes="[10, 20, 50, 100]" :total="totalSize"
                         @current-change="changePage"
                         @size-change="changeSize" background
                         layout="total, sizes, slot, jumper, prev, pager, next" v-show="totalSize > 10">
          </el-pagination>

        </el-tab-pane>
      </el-tabs>
    </el-row>

    <el-dialog :close-on-click-modal="closeMode" :visible.sync="showAdditionDialog" class="abort"
               title="补录备案信息" width="500px">
      <el-form :inline="true" :model="additionForm" :rules="additionFormRules" ref="additionForm" size="small"
               label-width="10px;">
        <el-form-item label="备案租期" prop="contractStartToEnd">
          <el-date-picker v-model="additionForm.contractStartToEnd" type="daterange" range-separator="至"
                          start-placeholder="备案起租日" value-format="yyyy-MM-dd"
                          end-placeholder="备案到期日" size="mini">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="租赁凭证编号" prop="ticketNumber">
          <el-input clearable v-model="additionForm.ticketNumber"/>
        </el-form-item>
      </el-form>
      <div class="dialog-footer" slot="footer">
        <el-button @click="closeAddition" size="small">取 消</el-button>
        <el-button :disabled="sending" :loading="sending" @click="saveAddition" size="small" type="primary">
          {{ sending ? '正在保存...' : '提 交' }}
        </el-button>
      </div>
    </el-dialog>

    <el-dialog :close-on-click-modal="closeMode" :visible.sync="showDialog" class="abort"
               title="协议终止" width="500px">
      <el-form :model="dataForm" :rules="dataRules" ref="dataForm" size="small">
        <el-form-item>

          <span style="color: red;">温馨提示:请选择<strong style="color: blue;">终止原因</strong>,并点击<strong style="color: green;">提交</strong>(只记录终止原因),提交后请前往结算管理进行本协议的费用结算,
            结算后本协议自动终止或到期.</span>
        </el-form-item>

        <el-form-item label="终止原因" prop="abortReasonId">
          <el-select clearable filterable placeholder="请选择终止原因" v-model="dataForm.abortReasonId">
            <el-option :key="index" :label="item.name" :value="item.id"
                       v-for="(item, index) in configs.terminationReasons"/>
          </el-select>
        </el-form-item>
      </el-form>
      <div class="dialog-footer" slot="footer">
        <el-button @click="cancle" size="small">取 消</el-button>
        <el-button :disabled="sending" :loading="sending" @click="abortConfirm" size="small" type="primary">
          {{ sending ? '正在保存...' : '提 交' }}
        </el-button>
      </div>
    </el-dialog>

    <el-dialog :close-on-click-modal="false" class="abort" :visible.sync="showInfoDialog"
               title="租户协议审核" width="1200px">
      <el-form class="data-form" :model="detailObj" ref="detailObj">
        <el-form-item>
          <el-input v-model="detailObj.agreementNumber" readonly>
            <template slot="prepend">租赁协议编号</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.zoneName" readonly>
            <template slot="prepend">区域</template>
          </el-input>
          <el-input v-model="detailObj.buildingName" readonly>
            <template slot="prepend">楼宇</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.buildingProvince + detailObj.buildingCity" readonly>
            <template slot="prepend">楼宇地址</template>
          </el-input>
          <el-input v-model="detailObj.buildingAddress" readonly>
            <template slot="prepend">详细地址</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.roomNum" readonly>
            <template slot="prepend">房号</template>
          </el-input>
        </el-form-item>
        <el-row>
          <el-form-item label="仪表底数" label-width="100px">
            <el-table :data="detailObj.meterList" style="width: 100%;margin-right: 10%" stripe border>
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
          <el-input v-model="detailObj.partyA" readonly>
            <template slot="prepend">出租方(甲方)</template>
          </el-input>
          <el-input v-model="detailObj.tenantName" readonly>
            <template slot="prepend">承租方(乙方)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aProvince + detailObj.aCity" readonly>
            <template slot="prepend">甲方地址</template>
          </el-input>
          <el-input v-model="detailObj.tenantProvince + detailObj.tenantCity" readonly>
            <template slot="prepend">乙方地址</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aAddress" readonly>
            <template slot="prepend">甲方详细地址</template>
          </el-input>
          <el-input v-model="detailObj.tenantAddress" readonly>
            <template slot="prepend">乙方详细地址</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aLinkman" readonly>
            <template slot="prepend">甲方联系人</template>
          </el-input>
          <el-input v-model="detailObj.contractor" readonly>
            <template slot="prepend">乙方联系人</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aContact" readonly>
            <template slot="prepend">甲方联系方式</template>
          </el-input>
          <el-input v-model="detailObj.contractorMobile" readonly>
            <template slot="prepend">乙方联系方式</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.aOrgCode" readonly>
            <template slot="prepend">甲方组织机构代码证</template>
          </el-input>
          <el-input v-model="detailObj.tenantOrgCode" readonly>
            <template slot="prepend">乙方身份证/组织机构代码证</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.managementType" readonly>
            <template slot="prepend">乙方经营类型</template>
          </el-input>
          <el-input v-model="detailObj.aOrgCode" readonly v-if="detailObj.tenantTypeId === 1">
            <template slot="prepend">乙方公司法人</template>
          </el-input>
        </el-form-item>
        <el-form-item label="甲方收款账户">
          <el-table :data="detailObj.accountList" style="width: 100%" stripe border>
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
          <el-input v-model="detailObj.contractDate" readonly>
            <template slot="prepend">签约日期</template>
          </el-input>
          <el-input v-model="'每月'+detailObj.leasePaymentDate+'日'" readonly>
            <template slot="prepend">约定交租日期</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.agreementStartAt + ' 至 ' +detailObj.agreementEndAt" readonly>
            <template slot="prepend">协议租期</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.contractStartToEnd" readonly>
            <template slot="prepend">备案租期</template>
          </el-input>
          <el-input v-model="detailObj.ticketNumber" readonly>
            <template slot="prepend">租赁凭证编号</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.freeStartToEnd" readonly>
            <template slot="prepend">免租期</template>
          </el-input>
          <el-input v-model="detailObj.incrementalState ? '有' : '无'" readonly>
            <template slot="prepend">有无递增周期</template>
          </el-input>
        </el-form-item>
        <el-form-item label="递增周期" label-width="300" v-if="detailObj.incrementalState">
          <el-table :data="detailObj.incrDetailsList" style="width: 100%" stripe border>
            <el-table-column label="递增区间" width="330px">
              <template slot-scope="scope">
                <el-input v-model="scope.row.incrPeriodStartAt + ' 至 ' +scope.row.incrPeriodEndAt" readonly/>
              </template>
            </el-table-column>
            <el-table-column label="递增比例(%)" width="140px">
              <template slot-scope="scope">
                <el-input clearable v-model="scope.row.incrRatio" type="number" size="mini" style="width: 70px;"
                          readonly/>
              </template>
            </el-table-column>
            <el-table-column label="递增单价(元/m²/月)" width="140px">
              <template slot-scope="scope">
                <el-input clearable v-model="scope.row.incrUnitPrice" type="number" size="mini" style="width: 70px;"
                          readonly/>
              </template>
            </el-table-column>
            <el-table-column label="递增金额(元)" width="140px">
              <template slot-scope="scope">
                <el-input clearable v-model="scope.row.incrFee" type="number" size="mini" style="width: 70px;"
                          readonly/>
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
                <el-input clearable v-model="scope.row.afterIncrFee" type="number" size="mini" style="width: 70px;"
                          readonly/>
              </template>
            </el-table-column>
          </el-table>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.rentArea" readonly>
            <template slot="prepend">出租面积(m²)</template>
          </el-input>
          <el-input v-model="detailObj.regArea" readonly>
            <template slot="prepend">备案面积(m²)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.leaseType" readonly>
            <template slot="prepend">租赁类型</template>
          </el-input>
          <el-input v-model="detailObj.agreementType" readonly>
            <template slot="prepend">协议类型</template>
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
          <el-input v-model="detailObj.airConditionFeesMethod" readonly>
            <template slot="prepend">空调费计算方式</template>
          </el-input>
          <el-input v-model="detailObj.airConditionFeesUnitPrice" readonly
                    v-if="detailObj.airConditionFeesMethodId === 2 || detailObj.airConditionFeesMethodId === 3">
            <template slot="prepend">计费单价</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.monthlyRentFee" readonly>
            <template slot="prepend">月租金(元/月)</template>
          </el-input>
          <el-input v-model="detailObj.rentCollectionMethod">
            <template slot="prepend">租金收取方式</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.depositsType" readonly>
            <template slot="prepend">房屋押金类型</template>
          </el-input>
          <el-input v-model="detailObj.deposits" readonly>
            <template slot="prepend">房屋押金</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.otherDepositListStr" readonly style="width: 615px">
            <template slot="prepend">其他押金(元)</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.decorationStatus?'是':'否'" readonly>
            <template slot="prepend">是否带装修</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.managementIncluded ? '是' : '否'" readonly>
            <template slot="prepend">是否包含管理费</template>
          </el-input>
          <el-input v-model="detailObj.managementFees" readonly v-if="!detailObj.managementIncluded">
            <template slot="prepend">管理费</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.taxIncluded ? '是' : '否'" readonly>
            <template slot="prepend">是否包含税</template>
          </el-input>
          <el-input v-model="detailObj.taxesFees" readonly v-if="!detailObj.taxIncluded">
            <template slot="prepend">税费</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.tenantSource" readonly>
            <template slot="prepend">成交信息来源</template>
          </el-input>
          <el-input v-model="detailObj.estCompany" readonly v-if="detailObj.tenantSourceId===1">
            <template slot="prepend">地产公司</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.estSalesman" readonly v-if="detailObj.tenantSourceId===1">
            <template slot="prepend">地产销售</template>
          </el-input>
          <el-input v-model="detailObj.estSlaesmanMobile" readonly v-if="detailObj.tenantSourceId===1">
            <template slot="prepend">地产销售手机号</template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="detailObj.remark" readonly>
            <template slot="prepend">备注</template>
          </el-input>
        </el-form-item>
        <el-table :data="arr" border stripe style="width: 100%">
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
import dict from 'utils/dict'
import { getLocalItem } from "utils/utils";
import {API_SERVER, IMG_SERVER} from "../../../config";
import {hasPermission} from "../../../permission";
import Qs from "qs";

export default {
  name: '',
  data() {
    return {
      arr: [],
      files: [],
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
      showAdditionDialog: false,
      showInfoDialog: false,
      closeMode: false,
      showDialog: false,
      sending: false,
      editing: false,
      page: 1,
      pageSize: 10,
      dataList: [],
      totalSize: 10,
      loading: false,
      detailObj: {},
      searchParams: {
        agreementStateId: '',
        buildingName: '',
      },
      dataForm: {
        id: "",
        abortReason: "",
        abortReasonId: "",
      },
      additionForm: {
        id: "",//协议编号
        agreementStartAt: "",//协议租期开始时间
        agreementEndAt: "",//协议租期结束时间
        contractStartToEnd: "",//备案租期
        contractStartAt: "",//备案租期开始
        contractEndAt: "",//备案租期结束
        ticketNumber: "",//租赁凭证编号
      },
      dataRules: {
        abortReasonId: [
          {required: true, message: '请选择终止原因', trigger: 'blur'}
        ]
      },
      additionFormRules: {
        contractStartToEnd: [
          {required: true, message: '备案租期不能为空', trigger: 'blur'}
        ],
        ticketNumber: [
          {required: true, message: '租赁凭证编号不能为空', trigger: 'blur'}
        ],
      },
      pages: {
        "0": [],
        "1": [],
        "2": [],
        "3": [],
        "4": [],
        "5": [],
        "6": []
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
      title: '合同管理/租户协议管理',
      subTitle: '合同管理/租户协议管理',
      action: 'listHtTenantAgreement'
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
    hasPerms: function (perms) {
      // 根据权限标识和外部指示状态进行权限判断
      return hasPermission(perms)
    },
    //样式
    tableRowClassName({row, rowIndex}) {
      return row.warning ? 'warning-row' : "";
    },

    //翻页
    changePage(pageNo) {
      this.page = pageNo;
      this.loadData();
    },
    changeSize(pageSize) {
      this.pageSize = pageSize;
      this.page = 1;
      this.loadData();
    },

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
      const result = await api.htTenantAgreement.listPage(params);
      this.loading = false;
      if (result.code === 200) {
        this.$nextTick(() => {
          result.data.records.forEach(item => {
            item.roomNum = item.roomNum.replace(/\[|]| |"/g, '')
          })
          this.pages[this.searchParams.agreementStateId] = result.data.records;
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
     * 新增
     */
    add() {
      this.$router.push({name: 'addHtTenantAgreement'});
    },

    /**
     * 编辑
     * @param row
     */
    edit(row) {
      this.$router.push({name: 'editHtTenantAgreement', params: {id: row.id}});
    },

    /**
     * 详情
     * @param row
     */
    info(row) {
      this.$router.push({name: 'viewHtTenantAgreement', params: {id: row.id}});
    },

    /**
     * 提交审核
     * @param row
     */
    submitReview(row) {
      this.$confirm("确定要提交审核此协议？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htTenantAgreement.submitReview(row.id);
          if (res.code === 200) {
            this.$message.success("提交成功");
            this.loadData();
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
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
     * 关闭弹窗
     */
    close() {
      this.showInfoDialog = false;
      this.detailObj = {};
    },

    /**
     * 打开审核弹窗
     * @param row
     * @returns {Promise<void>}
     */
    async toDialog(row) {
      this.showInfoDialog = true;
      const result = await api.htTenantAgreement.getById(row.id)
      if (result.code === 200) {
        this.detailObj = result.data;
        this.detailObj.roomNum = this.detailObj.roomNum.replace(/\[|]| |"/g, '')
        this.detailObj.freeStartToEnd = this.detailObj.freePeriod === 1 ? this.detailObj.freeStartAt + " 至 " + this.detailObj.freeEndAt : "无";
        this.detailObj.otherDepositListStr = this.showOtherDeposit(this.detailObj.otherDepositList)
        this.detailObj.contractStartToEnd = (this.detailObj.contractStartAt != null && this.detailObj.contractEndAt != null) ? (this.detailObj.contractStartAt + " 至 " + this.detailObj.contractEndAt) : "";
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
      }
    },

    /**
     * 通过审核
     * @param id
     */
    passAudit(id) {
      this.$confirm("确定要审核通过此协议？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htTenantAgreement.passAudit(id);
          if (res.code === 200) {
            this.$message.success("审核成功");
            this.loadData();
            this.showInfoDialog = false;
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
    },

    /**
     * 驳回审核
     * @param id
     */
    rejectReview(id) {
      this.$confirm("确定要驳回此协议？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await api.htTenantAgreement.rejectReview(id);
          if (res.code === 200) {
            this.$message.success("驳回成功");
            this.loadData();
            this.showInfoDialog = false;
          } else {
            this.$message.error(res.msg);
          }
        })
        .catch(() => {
        });
    },

    /**
     * 打开补录弹窗
     * @param row
     * @returns {Promise<void>}
     */
    async additions(row) {
      const result = await api.htTenantAgreement.getById(row.id)
      if (result.code === 200) {
        this.showAdditionDialog = true;
        const obj = result.data;
        this.additionForm.id = obj.id;
        this.additionForm.agreementEndAt = obj.agreementEndAt;
        this.additionForm.ticketNumber = "";
      } else {
        this.$message.error(result.msg);
      }
    },

    /**
     * 关闭补录弹窗
     */
    closeAddition() {
      this.showAdditionDialog = false;
      this.additionForm.id = "";
      this.additionForm.agreementEndAt = "";
      this.additionForm.contractStartToEnd = "";
      this.additionForm.ticketNumber = "";
      this.$refs['additionForm'].clearValidate();
    },

    /**
     * 保存补录信息
     */
    saveAddition() {
      this.$refs.additionForm.validate(async (valid) => {
        if (valid) {
          let flag = true;
          if (this.additionForm.contractStartToEnd[1] > this.additionForm.agreementEndAt) {
            flag = false;
            await this.$confirm("备案租期结束时间大于协议租期结束时间,是否确认保存?", '补录确认', {
              distinguishCancelAndClose: true,
              confirmButtonText: '确认',
              cancelButtonText: '取消'
            })
              .then(async () => {
                flag = true;
              })
              .catch(action => {
                flag = false;
              });
          }
          if (flag) {
            const params = Object.assign(
              {
                id: this.additionForm.id,
                contractStartAt: this.additionForm.contractStartToEnd[0],
                contractEndAt: this.additionForm.contractStartToEnd[1],
                ticketNumber: this.additionForm.ticketNumber,
              },
            )
            const result = await api.htTenantAgreement.updateAddition(params);
            if (result.code === 200) {
              this.$message.success('补录成功!');
              this.additionForm.id = "";
              this.additionForm.agreementStartAt = "";
              this.additionForm.agreementEndAt = "";
              this.additionForm.contractStartToEnd = "";
              this.additionForm.ticketNumber = "";
              this.showAdditionDialog = false;
              this.loadData();
            } else {
              this.$message.error(result.msg);
            }
          }
        }
      });
    },

    /**
     * 打开终止协议弹窗
     * @param row
     */
    abort(row) {
      this.showDialog = true;
      this.dataForm.id = row.id;
      this.dataForm.abortReasonId = 1;
    },

    renew(row) {
      this.$router.push({name: 'renewHtTenantAgreement', params: {rid: row.id}});
    },

    /**
     * 保存终止原因
     */
    abortConfirm() {
      this.$refs.dataForm.validate(async (valid) => {
        if (valid) {
          this.$confirm('确定要终止该协议吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(async () => {
            const params = Object.assign({}, this.dataForm);
            params.abortReason = dict.description(this.configs.terminationReasons, params.abortReasonId)
            const result = await api.htTenantAgreement.terminate(params);
            if (result.code === 200) {
              this.$message.success('更新成功!');
              this.showDialog = false;
              this.sending = false;
              this.loadData();
            } else {
              this.$message.error(result.msg);
            }
          }).catch(() => {
          });
        }
      });
    },

    /**
     * 终止框关闭
     */
    cancle() {
      this.showDialog = false;
      this.editing = false;
      this.$refs['dataForm'].clearValidate();
    },

    /**
     *
     */
    handleTabClick(tab, event) {
      this.loadData(1)
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
          strList.push(str)
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
  .el-table .warning-row {
    color: red;
  }

  .el-table .cell {
    white-space: pre-line;
  }
}
</style>
