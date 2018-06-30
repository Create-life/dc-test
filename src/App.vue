<template>
  <div class="container">
    <h2>债项模型计算</h2>
    <b-navbar type="dark" class="nav-info" variant="info">
      <div class="col-md-2 nav_item t-center">{{isCheck?'当前场景':'场景选择：'}}</div>
      <div class="col-md-9 nav_item row">
        <div class="col-md-4">
          <b-form-input v-show="!isCheck" placeholder="发行人数量" class="nav_input" type="number" name="companyNum" v-model="companyNum"></b-form-input>
          <span v-show="isCheck">发行人数量：<span class="notice">{{companyNum}}</span></span>
        </div>
        <div class="col-md-4">
          <b-form-input v-show="!isCheck" placeholder="担保人数量" type="number" name="guarantorNum" v-model="guarantorNum"></b-form-input>
          <span v-show="isCheck">担保人数量：<span class="notice">{{guarantorNum}}</span></span>
        </div>
        <div class="col-md-4">
          <b-form-input v-show="!isCheck" placeholder="抵质押品数量" type="number" name="pledgeNum" v-model="pledgeNum"></b-form-input>
          <span v-show="isCheck">抵质押品数量：<span class="notice">{{pledgeNum}}</span></span>
        </div>
      </div>
      <div class="col-md-1">
        <b-button v-show="!isCheck" @click="checkSelect" variant="primary">确定</b-button>
        <b-button v-show="isCheck" @click="reset" variant="secondary">重置</b-button>
      </div>
    </b-navbar>

    <!-- 债项信息 -->
    <b-navbar class="bond-info enter-list" v-if="bondTypeList">
      <div class="col-md-2 nav_item t-center">债项信息：</div>
      <div class="col-md-9 nav_item row" v-show="!bondTypeCheck">
        <div class="col-md-4">
          <label>债券余额：</label>
          <b-form-input @change="resultInit" class="nav_input" type="number" name="Company_nature" v-model="bondBalance"></b-form-input>
          <label>亿元</label>
        </div>
        <div class="col-md-8">
          <label>债项类型：</label>
          <b-form-select @change="resultInit" v-model="bondType" :options="bondTypeList.options" />
          <span class="notice">{{bondType.ratio}}</span>
        </div>
      </div>
      <div class="col-md-9 nav_item row" v-if="bondType" v-show="bondTypeCheck">
        <div class="col-md-4">
          <span>债券余额：</span>
          <span class="notice">{{bondBalance?bondBalance:'-'}}</span>
          <span>亿元</span>
        </div>
        <div class="col-md-8">
          <label>债项类型：</label>
          <span class="text-overflow">{{bondType.name}}</span>
          <span class="notice">{{bondType.ratio}}</span>
        </div>
      </div>
      <div class="col-md-1">
        <b-button variant="outline-primary" v-show="!bondTypeCheck" @click="checkBondType">确定</b-button>
        <b-button variant="outline-secondary" v-show="bondTypeCheck" @click="resetBondType">重置</b-button>
      </div>
    </b-navbar>

    <!-- 发行人信息 -->
    <b-navbar class="company-info enter-list" v-b-modal="`company${idx}`" v-if="companyList.length>0" v-for="(company, idx) in companyList" :key="company.id">
      <div class="col-md-2 nav_item t-center">发行人信息{{companyNum>1?idx+1:''}}：</div>
      <div class="col-md-10 nav_item info">
        <div class="row">
          <div class="col-md-6 flex">
            <label>{{companyNatureList.factorName}}：</label>
            <span class="text-overflow" :title="company.companyNature.name">{{company.companyNature.name}}</span>
            <span class="notice">{{company.companyNature.ratio}}</span>
          </div>
          <div class="col-md-6 flex">
            <label>{{industaryList.factorName}}：</label>
            <span class="text-overflow" :title="company.industary.name">{{company.industary.name}}</span>
            <span class="notice">{{company.industary.ratio}}</span>
          </div>
          <div class="col-md-6 flex">
            <label>{{creditRegionList.factorName}}：</label>
            <span class="text-overflow" :title="company.creditRegion.name">{{company.creditRegion.name}}</span>
            <span class="notice">{{company.creditRegion.ratio}}</span>
          </div>
          <div class="col-md-6 flex" v-if="companyNum==1">
            <label>发行人有效认定评级：</label>
            <span class="notice">{{company.companyRating.rating}}</span>
          </div>
          <div class="col-md-6 flex" v-if="companyNum>1">
            <label>发行人违约率：</label>
            <span class="notice">{{company.companyPd?company.companyPd:'-'}}%</span>
          </div>
        </div>
      </div>
      <!-- 发行人模态框 -->
      <b-modal ref="companyModal" centered :id="`company${idx}`" hide-footer size="lg" :title="`发行人${companyNum>1?idx + 1:''}信息修改`">
        <p class="my-1 flex">
          <label>{{companyNatureList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="company.companyNature" :options="companyNatureList.options" />
          <span class="notice">{{company.companyNature.ratio}}</span>
        </p>
        <p class="my-1 flex">
          <label>{{industaryList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="company.industary" :options="industaryList.options" />
          <span class="notice">{{company.industary.ratio}}</span>
        </p>
        <p class="my-1 flex">
          <label>{{creditRegionList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="company.creditRegion" :options="creditRegionList.options" />
          <span class="notice">{{company.creditRegion.ratio}}</span>
        </p>
        <p class="my-1 flex" v-if="companyList.length==1">
          <label>发行人有效认定评级：</label>
          <b-form-select @change="resultInit" v-model="company.companyRating" :options="scaleList" />
          <span class="notice"></span>
        </p>
        <p class="my-1 flex" v-if="companyList.length>1">
          <label>发行人违约率：</label>
          <b-form-input @change="resultInit" type="number" name="companyPd" v-model="company.companyPd"></b-form-input>
          <span class="notice"></span>
        </p>
        <b-btn class="mt-3" variant="outline-success" block @click="hideCompanyModal(idx)">OK</b-btn>
      </b-modal>
    </b-navbar>

    <!-- 担保人信息 -->
    <b-navbar class="guarantor-info enter-list" v-b-modal="`warrantor${idx}`" v-if="warrantorList.length>0" v-for="(warrantor, idx) in warrantorList" :key="warrantor.id">
      <div class="col-md-2 nav_item t-center">担保人信息{{guarantorNum>1?idx+1:''}}：</div>
      <div class="col-md-10 nav_item info">
        <div class="row">

          <div class="col-md-3 flex">
            <label>担保金额：</label>
            <span class="notice">{{warrantor.warrantorPrice?warrantor.warrantorPrice:'-'}}</span>
            <span>亿元</span>
          </div>

          <div class="col-md-3 flex" v-if="guarantorNum==1">
            <label>担保人有效认定评级：</label>
            <span class="notice">{{warrantor.guarantorRating.rating}}</span>
          </div>
          <div class="col-md-3 flex" v-if="guarantorNum>1">
            <label>担保人违约率：</label>
            <span class="notice">{{warrantor.guarantorPd?warrantor.guarantorPd:'-'}}%</span>
          </div>

          <div class="col-md-6 flex">
            <label>{{guaranteeTypeList.factorName}}：</label>
            <span class="text-overflow" :title="warrantor.guaranteeType.name">{{warrantor.guaranteeType.name}}</span>
            <span class="notice">{{warrantor.guaranteeType.ratio}}</span>
          </div>

          <div class="col-md-6 flex">
            <label>{{warrantorTypeList.factorName}}：</label>
            <span class="text-overflow" :title="warrantor.warrantorType.name">{{warrantor.warrantorType.name}}</span>
            <span class="notice">{{warrantor.warrantorType.ratio}}</span>
          </div>

          <div class="col-md-6 flex">
            <label>{{warrantyStrengthList.factorName}}：</label>
            <span class="text-overflow" :title="warrantor.warrantyStrength.name">{{warrantor.warrantyStrength.name}}</span>
            <span class="notice">{{warrantor.warrantyStrength.ratio}}</span>
          </div>
        </div>
      </div>
      <!-- 担保人模态框 -->
      <b-modal ref="warrantorModal" centered :id="`warrantor${idx}`" hide-footer size="lg" :title="`担保人${guarantorNum>1?idx + 1:''}信息修改`">
        <p class="my-1 flex">
          <label>担保金额：</label>
          <b-form-input @change="resultInit" class="nav_input" type="number" name="warrantorPrice" v-model="warrantor.warrantorPrice"></b-form-input>
          <span class="notice"></span>
        </p>

        <p class="my-1 flex" v-if="guarantorNum==1">
          <label>担保人有效认定评级：</label>
          <b-form-select @change="resultInit" v-model="warrantor.guarantorRating" :options="scaleList" />
          <span class="notice"></span>
        </p>
        <p class="my-1 flex" v-if="guarantorNum>1">
          <label>担保人违约率：</label>
          <b-form-input @change="resultInit" type="number" v-model="warrantor.guarantorPd"></b-form-input>
          <span class="notice"></span>
        </p>

        <p class="my-1 flex">
          <label>{{guaranteeTypeList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="warrantor.guaranteeType" :options="guaranteeTypeList.options" />
          <span class="notice">{{warrantor.guaranteeType.ratio}}</span>
        </p>

        <p class="my-1 flex">
          <label>{{warrantorTypeList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="warrantor.warrantorType" :options="warrantorTypeList.options" />
          <span class="notice">{{warrantor.warrantorType.ratio}}</span>
        </p>

        <p class="my-1 flex">
          <label>{{warrantyStrengthList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="warrantor.warrantyStrength" :options="warrantyStrengthList.options" />
          <span class="notice">{{warrantor.warrantyStrength.ratio}}</span>
        </p>

        <b-btn class="mt-3" variant="outline-success" block @click="hideWarrantorModal(idx)">OK</b-btn>
      </b-modal>
    </b-navbar>

    <!-- 抵质押品信息 -->
    <b-navbar class="pledge-info enter-list" v-b-modal="`pledge${idx}`" v-if="pledgeList.length>0" v-for="(pledge, idx) in pledgeList" :key="pledge.id">
      <div class="col-md-2 nav_item t-center">抵质押品信息{{pledgeNum>1?idx+1:''}}：</div>
      <div class="col-md-10 nav_item info">
        <div class="row">
          <div class="col-md-3 flex">
            <label>抵质押品金额：</label>
            <span class="notice">{{pledge.pledgePrice?pledge.pledgePrice:'-'}}</span>
            <span>亿元</span>
          </div>
          <!-- 执法环境 -->
          <div class="col-md-4 flex">
            <label>{{pledgeRegionList.factorName}}：</label>
            <span class="text-overflow" :title="pledge.pledgeRegion.name">{{pledge.pledgeRegion.name}}</span>
            <span class="notice">{{pledge.pledgeRegion.ratio}}</span>
          </div>
          <!-- 抵押品独立性 -->
          <div class="col-md-5 flex">
            <label>{{pledgeDependList.factorName}}：</label>
            <span class="text-overflow" :title="pledge.pledgeDepend.name">{{pledge.pledgeDepend.name}}</span>
            <span class="notice">{{pledge.pledgeDepend.ratio}}</span>
          </div>
          <!-- 抵质押控制力 -->
          <div class="col-md-5 flex">
            <label>{{pledgeControlList.factorName}}：</label>
            <span class="text-overflow" :title="pledge.pledgeControl.name">{{pledge.pledgeControl.name}}</span>
            <span class="notice">{{pledge.pledgeControl.ratio}}</span>
          </div>
          <!-- 抵质押类型 -->
          <div class="col-md-7 flex">
            <label>{{pledgeTypeList.factorName}}：</label>
            <span class="text-overflow" :title="pledge.pledgeType.name">{{pledge.pledgeType.name}}</span>
            <span class="notice">{{pledge.pledgeType.ratio}}</span>
          </div>

        </div>
      </div>
      <!-- 抵质押品模态框 -->
      <b-modal ref="pledgeModal" centered :id="`pledge${idx}`" hide-footer size="lg" :title="`抵质押品${pledgeNum>1?idx + 1:''}信息修改`">
        <p class="my-1 flex">
          <label>抵质押品金额：</label>
          <b-form-input @change="resultInit" class="nav_input" type="number" name="pledgePrice" v-model="pledge.pledgePrice"></b-form-input>
          <span class="notice"></span>
        </p>
        <!-- 执法环境 -->
        <p class="my-1 flex">
          <label>{{pledgeRegionList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="pledge.pledgeRegion" :options="pledgeRegionList.options" />
          <span class="notice">{{pledge.pledgeRegion.ratio}}</span>
        </p>
        <!-- 抵押品独立性 -->
        <p class="my-1 flex">
          <label>{{pledgeDependList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="pledge.pledgeDepend" :options="pledgeDependList.options" />
          <span class="notice">{{pledge.pledgeDepend.ratio}}</span>
        </p>
        <!-- 抵质押控制力 -->
        <p class="my-1 flex">
          <label>{{pledgeControlList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="pledge.pledgeControl" :options="pledgeControlList.options" />
          <span class="notice">{{pledge.pledgeControl.ratio}}</span>
        </p>
        <!-- 抵质押类型 -->
        <p class="my-1 flex">
          <label>{{pledgeTypeList.factorName}}：</label>
          <b-form-select @change="resultInit" v-model="pledge.pledgeType" :options="pledgeTypeList.options" />
          <span class="notice">{{pledge.pledgeType.ratio}}</span>
        </p>

        <b-btn class="mt-3" variant="outline-success" block @click="hidePledgeModal(idx)">OK</b-btn>
      </b-modal>
    </b-navbar>

    <b-btn class="mt-3" size="lg" variant="warning" block @click="getResult">计算</b-btn>

    <div class="result row">
      <div class="col-md-4">
        <label>债项特征调整系数：</label>
        <span class="notice">{{bondFeatureAdjustCoefficient?bondFeatureAdjustCoefficient:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>原始的基础回收率：</label>
        <span class="notice">{{originalBasisRecoveryRate?originalBasisRecoveryRate:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>债券基础评级：</label>
        <span class="notice">{{companyBasisRating && companyBasisRating.rating?companyBasisRating.rating:'-'}}</span>
        </div>

      <div class="col-md-4">
        <label>债务人特征调整系数：</label>
        <span class="notice">{{debtorFeatureAdjustCoefficient?debtorFeatureAdjustCoefficient:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>基础回收率：</label>
        <span class="notice">{{basisRecoveryRate?basisRecoveryRate:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>担保人有效评级：</label>
        <span class="notice">{{guarantorRating && guarantorRating.rating?guarantorRating.rating:'-'}}</span>
        </div>

      <div class="col-md-4">
        <label>抵质押品缓释价值：</label>
        <span class="notice">{{pledgeReleasePrice?pledgeReleasePrice:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>LGD值：</label>
        <span class="notice">{{LGDValue?LGDValue:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>最终评级：</label>
        <span class="notice">{{finallyRating?finallyRating:'-'}}</span>
        </div>


      <div class="col-md-4">
        <label>担保人缓释价值：</label>
        <span class="notice">{{warrantorReleasePrice?warrantorReleasePrice:'-'}}</span>
        </div>
      <div class="col-md-4">
        <label>LGD级别：</label>
        <span class="notice">{{LGDObj && LGDObj.level?LGDObj.level:'-'}}</span>
        </div>


    </div>
  </div>
</template>

<script>
import axios from 'axios'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
import './app.css'
export default {
  name: "App",
  data() {
    return {
      companyNum: null, // 发行人信息
      pledgeNum: null, // 抵质押品数量
      guarantorNum: null, // 担保人数量
      isCheck: false, // 确认场景

      bondType: null, // 选取的债项类型
      bondTypeList: null,
      bondBalance: null, // 债券余额
      bondTypeCheck: false, // 债项类型确认


      companyNatureList: null, // 股权结构
      industaryList: null, // 发行人行业
      creditRegionList: null, // 信用环境
      warrantorTypeList: null, // 担保人类型
      warrantyStrengthList: null, // 担保强度
      guaranteeTypeList: null, // 担保类型
      pledgeTypeList: null, // 抵质押品类型
      pledgeControlList: null, // 抵质押品控制力
      pledgeRegionList: null, // 抵质押品执法环境
      pledgeDependList: null, // 抵质押物独立性

      LGDRules: null,
      scaleList: null, // 评级映射表

      companyList: [], // 发行人列表
      pledgeList: [], // 抵质押列表
      warrantorList: [], // 担保人列表

      // 计算结果：
      bondFeatureAdjustCoefficient: null, // 债项特征调整系数
      debtorFeatureAdjustCoefficient: null, // 债务人特征调整系数
      pledgeReleasePrice: null, // 抵质押品缓释价值
      warrantorReleasePrice: null, // 担保人缓释价值
      originalBasisRecoveryRate: null, // 原始的基础回收率
      basisRecoveryRate: null, // 基础回收率
      LGDValue: null, // LGD值
      LGDObj: null, // 匹配的LGD对象

      companyBasisRating: null, // 债券基础评级
      guarantorRating: null, // 担保人有效评级
      finallyRating: null, // 最终评级
    };
  },
  created() {
    axios({ url: "./static/data/company/BOND_TYPE.json" }).then(res => {
      this.bondTypeList = res.data;
      this.optionsEach(this.bondTypeList.options);
      this.bondType = this.bondTypeList.options[0];
    });
    axios({ url: "./static/data/company/CORP_NATURE.json" }).then(res => {
      this.companyNatureList = res.data;
      this.optionsEach(this.companyNatureList.options);
      this.companyNature = this.companyNatureList.options[0];
    });
    axios({ url: "./static/data/company/INDUSTRY.json" }).then(res => {
      this.industaryList = res.data;
      this.optionsEach(this.industaryList.options);
      this.industary = this.industaryList.options[0];
    });
    axios({ url: "./static/data/company/CREDIT_REGION.json" }).then(
      res => {
        this.creditRegionList = res.data;
        this.optionsEach(this.creditRegionList.options);
        this.creditRegion = this.creditRegionList.options[0];
      }
    );
    axios({ url: "./static/data/guarantor/WARRANTOR_TYPE.json" }).then(
      res => {
        this.warrantorTypeList = res.data;
        this.optionsEach(this.warrantorTypeList.options);
        this.warrantorType = this.warrantorTypeList.options[0];
      }
    );
    axios({ url: "./static/data/guarantor/WARRANTY_STRENGTH.json" }).then(
      res => {
        this.warrantyStrengthList = res.data;
        this.optionsEach(this.warrantyStrengthList.options);
        this.warrantyStrength = this.warrantyStrengthList.options[0];
      }
    );
    axios({ url: "./static/data/guarantor/GUARANTEE_TYPE.json" }).then(
      res => {
        this.guaranteeTypeList = res.data;
        this.optionsEach(this.guaranteeTypeList.options);
        this.guaranteeType = this.guaranteeTypeList.options[0];
      }
    );
    axios({ url: "./static/data/pledge/PLEDGE_TYPE.json" }).then(res => {
      this.pledgeTypeList = res.data;
      this.optionsEach(this.pledgeTypeList.options);
      this.pledgeType = this.pledgeTypeList.options[0];
    });
    axios({ url: "./static/data/pledge/PLEDGE_CONTROL.json" }).then(
      res => {
        this.pledgeControlList = res.data;
        this.optionsEach(this.pledgeControlList.options);
        this.pledgeControl = this.pledgeControlList.options[0];
      }
    );
    axios({ url: "./static/data/pledge/PLEDGE_REGION.json" }).then(
      res => {
        this.pledgeRegionList = res.data;
        this.optionsEach(this.pledgeRegionList.options);
        this.pledgeRegion = this.pledgeRegionList.options[0];
      }
    );
    axios({ url: "./static/data/pledge/PLEDGE_DEPEND.json" }).then(
      res => {
        this.pledgeDependList = res.data;
        this.optionsEach(this.pledgeDependList.options);
        this.pledgeDepend = this.pledgeDependList.options[0];
      }
    );
    axios({ url: "./static/data/LGDRules.json" }).then(
      res => {
        this.LGDRules = res.data;
      }
    );
    axios({ url: "./static/data/scales.json" }).then(
      res => {
        this.scaleList = res.data;
        this.scaleList.forEach(it => {
          it.value = it;
          it.text = it.rating;
        })
      }
    );
  },
  methods: {
    hidePledgeModal(idx) {
      this.$refs.pledgeModal[idx].hide()
    },
    hideWarrantorModal(idx) {
      this.$refs.warrantorModal[idx].hide()
    },
    hideCompanyModal(idx) {
      this.$refs.companyModal[idx].hide()
    },
    optionsEach(arr) {
      arr.forEach(it => {
        it.value = it;
        it.text = it.name;
      })
    },
    resetBondType() {
      this.bondTypeCheck = false;
    },
    checkBondType() {
      this.bondTypeCheck = true;
    },
    reset() {
      this.isCheck = false;
      this.companyNum = null;
      this.pledgeNum = null;
      this.guarantorNum = null;
      this.companyList = [];
      this.pledgeList = [];
      this.warrantorList = [];
    },
    checkSelect() {
      this.isCheck = true;
      this.companyNum = this.companyNum ? this.companyNum : 1;
      this.pledgeNum = this.pledgeNum ? this.pledgeNum : 0;
      this.guarantorNum = this.guarantorNum ? this.guarantorNum : 0;
      this.modelInit();
    },
    // 获取场景
    modelInit() {
      // 计算结果初始化
      this.resultInit();

      this.companyList = [];
      this.pledgeList = [];
      this.warrantorList = [];
      // 发行人
      for(var i = 0; i < this.companyNum; i++) {
        var companyObj = {
          id: `company${i}`,
          companyNature: this.companyNatureList.options[0],
          industary: this.industaryList.options[0],
          creditRegion: this.creditRegionList.options[0],
          companyPd: null, // 违约率
          companyRating: this.scaleList[0]  // 发行人评级
        }
        this.companyList.push(companyObj);
      }
      // 抵押品
      for(var i = 0; i < this.pledgeNum; i++) {
        var pledgeObj = {
          id: `pledge${i}`,
          pledgePrice: null,
          pledgeDepend: this.pledgeDependList.options[0],
          pledgeRegion: this.pledgeRegionList.options[0],
          pledgeControl: this.pledgeControlList.options[0],
          pledgeType: this.pledgeTypeList.options[0]
        }
        this.pledgeList.push(pledgeObj);
      }

      // 担保人
      for(var i = 0; i < this.guarantorNum; i++) {
        var warrantorObj = {
          id: `guarantor${i}`,
          warrantorPrice: null,
          guaranteeType: this.guaranteeTypeList.options[0],
          warrantorType: this.warrantorTypeList.options[0],
          warrantyStrength: this.warrantyStrengthList.options[0],
          guarantorPd: null, // 担保人违约率
          guarantorRating: this.scaleList[0] // 担保人评级
        }
        this.warrantorList.push(warrantorObj);
      }
    },
    resultInit() {
      this.bondFeatureAdjustCoefficient = null;
      this.debtorFeatureAdjustCoefficient = null;
      this.pledgeReleasePrice = null;
      this.warrantorReleasePrice = null;
      this.originalBasisRecoveryRate = null;
      this.basisRecoveryRate = null;
      this.LGDValue = null;
      this.LGDObj = null;
      this.companyBasisRating = null;
      this.guarantorRating = null;
      this.finallyRating = null;
    },
    getResult() {
      this.resultInit();
      // 债项特征调整系数
      this.getBondFeatureAdjustCoefficient();
      // 债务人特征调整系数
      this.getDebtorFeatureAdjustCoefficient();
      // 抵质押品缓释价值
      this.getPledgeReleasePrice();
      // 担保人缓释价值
      this.getWarrantorReleasePrice();
      // 原始的基础回收率
      this.getOriginalBasisRecoveryRate();
      // 基础回收率
      this.getBasisRecoveryRate();
      // LGD值
      this.getLGDValue();
      // LGD映射
      this.getLGDLevel();
      // 债券基础评级
      this.getCompanyBasisRating();
      // 担保人有效评级
      this.getGuaranterRating();
      // 债券最终评级
      this.getFinallyRating();
    },
    // 债项特征调整系数:等于债项类型
    getBondFeatureAdjustCoefficient() {
      this.bondFeatureAdjustCoefficient = this.bondType.ratio;
    },
    // 债务人特征调整系数=股权结构*发行人行业*信用环境
    getDebtorFeatureAdjustCoefficient() {
      var temp = 0;
      for(var i = 0; i< this.companyNum; i++) {
        var item = this.companyList[i];
        temp += (item.companyNature.ratio * 1000) * (item.industary.ratio * 1000) * (item.creditRegion.ratio * 1000);
      }
      this.debtorFeatureAdjustCoefficient = temp / 1000000000 / this.companyNum;
    },
    // 抵质押品缓释价值=抵质押品价值*抵质押品类型*抵质押品控制力*抵质押品执法环境
    getPledgeReleasePrice() {
      for(var i = 0;i < this.pledgeList.length; i++) {
        var item = this.pledgeList[i];
        this.pledgeReleasePrice += (item.pledgePrice * 1000) * (item.pledgeType.ratio * 1000) * (item.pledgeControl.ratio * 1000) * (item.pledgeRegion.ratio * 1000) / 1000000000000;
      }
    },
    // 担保人缓释价值=担保人类型*担保强度*担保价值*担保类型
    getWarrantorReleasePrice() {
      for(var i = 0;i < this.warrantorList.length; i++) {
        var item = this.warrantorList[i];
        this.warrantorReleasePrice += (item.warrantorType.ratio * 1000) * (item.warrantyStrength.ratio * 1000) * (item.warrantorPrice * 1000) * (item.guaranteeType.ratio * 1000) / 1000000000000;
      }
    },
    // 原始的基础回收率:担保人缓释价值+抵质押品缓释价值大于0，那么就等于(担保人缓释价值+抵质押品缓释价值)/债券风险暴露EAD,否则就等于0.35
    getOriginalBasisRecoveryRate() {
      var temp = this.warrantorReleasePrice + this.pledgeReleasePrice
      if ( temp > 0) {
        this.originalBasisRecoveryRate = temp / this.bondBalance;
      } else {
        this.originalBasisRecoveryRate = 0.35;
      }
    },
    // 基础回收率（上限1.05）:如果【原始的基础回收率】小于1.05那么就等于原始的基础回收率，否则就等于1.05
    getBasisRecoveryRate() {
      if (this.originalBasisRecoveryRate < 1.05) {
        this.basisRecoveryRate = this.originalBasisRecoveryRate;
      } else {
        this.basisRecoveryRate = 1.05;
      }
    },
    // LGD值=1-基础回收率（上限1.05）*债务人特征调整系数*债项特征调整系数
    getLGDValue() {
      this.LGDValue = 1 - this.basisRecoveryRate * this.debtorFeatureAdjustCoefficient * this.bondFeatureAdjustCoefficient;
    },
    // LGD级别
    getLGDLevel() {
      this.LGDRules.some(item => {
        if (this.LGDValue > item.lowBound && this.LGDValue <= item.upperBound) {
          this.LGDObj = item;
          return;
        }
      })
    },
    // 债券基础评级：
    getCompanyBasisRating() {
      if (this.companyNum > 1) {
        // 平均违约率
        var averagePd = 0;
        this.companyList.forEach(it => {
          averagePd += it.companyPd * 1000;
        })
        averagePd = averagePd / this.companyNum / 100000;
        this.companyList[0].companyRating = this.scaleList.filter(it => { // 发行人评级
          return averagePd > it.minValue && averagePd <= it.maxValue;
        })[0];
      }
      var temp = this.companyList[0].companyRating.id - this.LGDObj.adjust;
      if (temp < 1) {
        temp = 1;
      }
      this.companyBasisRating = this.scaleList.filter(it => {
        return it.id === temp
      })[0];
    },
    // 担保人有效评级
    getGuaranterRating() {
      if (this.guarantorNum > 0) {
        if (this.guarantorNum > 1) {
          // 平均违约率
          var averagePd = 0;
          this.warrantorList.forEach(it => {
            averagePd += it.guarantorPd * 1000;
          })
          averagePd = averagePd / this.warrantorList.length / 100000;
          this.warrantorList[0].guarantorRating = this.scaleList.filter(it => { // 发行人评级
            return averagePd > it.minValue && averagePd <= it.maxValue;
          })[0];
        }
        this.guarantorRating = this.warrantorList[0].guarantorRating;
      }
    },
    // 最终评级
    getFinallyRating() {
      if (!this.guarantorRating) {
        this.finallyRating = this.companyBasisRating.rating;
      } else {
        this.finallyRating = this.companyBasisRating.id < this.guarantorRating.id ? this.companyBasisRating.rating : this.guarantorRating.rating;
      }
    }
  }
};


</script>

