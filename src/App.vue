<template>
  <div class="container">
    <div class="left">
      <h2>债项模型计算：</h2>

      <div>
        <h3 style="display: inline-block;">选择场景：</h3>
        <div>
          <div>
            <label>发行人数量：</label>
            <input type="number" name="companyNum" v-model="companyNum" @change="modelChange">
          </div>
          <div style="margin-top: 10px;">
            <label>担保人数量：</label>
            <input type="number" name="guarantorNum" v-model="guarantorNum" @change="modelChange">
          </div>
          <div style="margin-top: 10px;">
            <label>抵质押品数量：</label>
            <input type="number" name="pledgeNum" v-model="pledgeNum" @change="modelChange" style="margin-right: 20px;" >
          </div>
        </div>
      </div>
      <hr style="margin-top: 20px;">

      <!-- 债项信息 -->
      <div>
        <h3>债项信息</h3>
        <div class="form-group">
          <label>债券余额：</label>
          <input type="number" name="Company_nature" v-model="bondBalance"/>亿元
          <span class="notice">{{bondBalance}}</span>
        </div>
        <!-- 债项类型 -->
        <div class="form-group" v-if="bondType">
          <label>{{bondType.factorName}}：</label>
          <select name="Company_nature" v-model="bondTypeRatio">
            <option :value="item.ratio" v-for="item in bondType.options" :key="item.id">{{item.name}}</option>
          </select>
          <span class="notice">{{bondTypeRatio}}</span>
        </div>
      </div>

      <!-- 发行人信息 -->
      <div>
        <hr style="margin-top: 20px;">
        <h3>发行人信息</h3>
        <!-- 发行人列表 -->
        <div v-for="(company, index) in companyList" :key="index" >
          <h4 style="margin-top: 10px;">发行人{{index+1}}</h4>
          <!-- 企业性质 -->
          <div class="form-group" v-if="companyNature">
            <label>{{companyNature.factorName}}：</label>
            <select name="Company_nature" v-model="company.companyNatureRatio">
              <option v-for="item in companyNature.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{company.companyNatureRatio}}</span>
          </div>
          <!-- 发行人行业 -->
          <div class="form-group" v-if="industary">
            <label>{{industary.factorName}}：</label>
            <select name="Company_nature" v-model="company.industaryRatio">
              <option v-for="item in industary.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{company.industaryRatio}}</span>
          </div>
          <!-- 信用环境 -->
          <div class="form-group" v-if="creditRegion">
            <label>{{creditRegion.factorName}}：</label>
            <select name="Company_nature" v-model="company.creditRegionRatio">
              <option v-for="item in creditRegion.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{company.creditRegionRatio}}</span>
          </div>
          <!-- 发行人评级 -->
          <div class="form-group" v-if="companyNum==1">
            <label>发行人有效认定评级：</label>
            <select name="Company_nature" v-model="companyScale">
              <option v-for="item in scaleList" :key="item.id" :value="item.id">{{item.rating}}</option>
            </select>
          </div>
          <div class="form-group" v-if="companyNum>1">
            <label>发行人违约率：</label>
            <input type="number" name="companyPd" v-model="company.companyPd">%
          </div>

        </div>
      </div>

      <!-- 抵质押品信息 -->
      <div v-if="pledgeList.length > 0">
        <hr style="margin-top: 20px;">
        <h3>抵质押品信息</h3>
        <!-- 抵质押品列表 -->
        <div v-for="(pledge, index) in pledgeList" :key="index" >
          <h4 style="margin-top: 10px;">抵质押品{{index+1}}</h4>
          <div class="form-group">
            <label>抵制押金额：</label>
            <input type="number" name="Company_nature" v-model="pledge.pledgePrice"/>
            <span class="notice">{{pledge.pledgePrice}}</span>
          </div>
          <!-- 抵质押品独立性 -->
          <div class="form-group" v-if="pledgeDepend">
            <label>{{pledgeDepend.factorName}}：</label>
            <select :name="'Company_nature' + index" v-model="pledge.pledgeDependRatio">
              <option v-for="item in pledgeDepend.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{pledge.pledgeDependRatio}}</span>
          </div>
          <!-- 抵质押品类型 -->
          <div class="form-group" v-if="pledgeType">
            <label>{{pledgeType.factorName}}：</label>
            <select name="Company_nature" v-model="pledge.pledgeTypeRatio">
              <option v-for="item in pledgeType.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{pledge.pledgeTypeRatio}}</span>
          </div>
          <!-- 抵质押品控制力 -->
          <div class="form-group" v-if="pledgeControl">
            <label>{{pledgeControl.factorName}}：</label>
            <select name="Company_nature" v-model="pledge.pledgeControlRatio">
              <option v-for="item in pledgeControl.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{pledge.pledgeControlRatio}}</span>
          </div>
          <!-- 抵质押品执法环境 -->
          <div class="form-group" v-if="pledgeRegion">
            <label>{{pledgeRegion.factorName}}：</label>
            <select name="Company_nature" v-model="pledge.pledgeRegionRatio">
              <option v-for="item in pledgeRegion.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{pledge.pledgeRegionRatio}}</span>
          </div>
        </div>
      </div>

      <!-- 担保人信息 -->
      <div v-if="warrantorList.length > 0">
        <hr style="margin-top: 20px;">
        <h3>担保人信息</h3>
        <!-- 担保人列表 -->
        <div v-for="(warrantor, index) in warrantorList" :key="index" >
          <h4 style="margin-top: 10px;">担保人{{index+1}}</h4>
          <!-- 担保金额 -->
          <div class="form-group">
            <label>担保金额：</label>
            <input type="number" name="Company_nature" v-model="warrantor.warrantorPrice"/>
            <span class="notice">{{warrantor.warrantorPrice}}</span>
          </div>
          <!-- 担保类型 -->
          <div class="form-group" v-if="guaranteeType">
            <label>{{guaranteeType.factorName}}：</label>
            <select :name="'Company_nature' + index" v-model="warrantor.guaranteeTypeRatio">
              <option v-for="item in guaranteeType.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{warrantor.guaranteeTypeRatio}}</span>
          </div>
          <!-- 担保人类型 -->
          <div class="form-group" v-if="warrantorType">
            <label>{{warrantorType.factorName}}：</label>
            <select name="Company_nature" v-model="warrantor.warrantorTypeRatio">
              <option v-for="item in warrantorType.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{warrantor.warrantorTypeRatio}}</span>
          </div>
          <!-- 担保强度 -->
          <div class="form-group" v-if="warrantyStrength">
            <label>{{warrantyStrength.factorName}}：</label>
            <select name="Company_nature" v-model="warrantor.warrantyStrengthRatio">
              <option v-for="item in warrantyStrength.options" :key="item.id" :value="item.ratio">{{item.name}}</option>
            </select>
            <span class="notice">{{warrantor.warrantyStrengthRatio}}</span>
          </div>
          <!-- 担保人评级 -->
          <div class="form-group" v-if="guarantorNum==1">
            <label>担保人有效认定评级 ：</label>
            <select v-model="guarantorScale">
              <option v-for="item in scaleList" :key="item.id" :value="item.id">{{item.rating}}</option>
            </select>
          </div>
          <div class="form-group" v-if="guarantorNum>1">
            <label>发行人违约率：</label>
            <input type="number" name="guarantorPd" v-model="warrantor.guarantorPd">%
          </div>
        </div>
      </div>
    </div>

    <div class="result" id="result">
      <div class="btn" @click="getResult">获取计算结果</div>
      <div v-if="LGDLevel">
        <h3>计算结果：</h3>
        <div class="form-group">
        <span>债项特征调整系数</span> ：<span style="color: red;">{{bondFeatureAdjustCoefficient}}</span>
        </div>
        <div class="form-group">
          <span>债务人特征调整系数</span> ：<span style="color: red;">{{debtorFeatureAdjustCoefficient}}</span>
        </div>
        <div class="form-group">
          <span>抵质押品缓释价值</span> ：<span style="color: red;">{{pledgeReleasePrice}}</span>
        </div>
        <div class="form-group">
          <span>担保人缓释价值</span> ：<span style="color: red;">{{warrantorReleasePrice}}</span>
        </div>
        <div class="form-group">
          <span>原始的基础回收率</span> ：<span style="color: red;">{{originalBasisRecoveryRate}}</span>
        </div>
        <div class="form-group">
          <span>基础回收率</span> ：<span style="color: red;">{{basisRecoveryRate}}</span>
        </div>
        <div class="form-group">
          <span>LGD值</span> ：<span style="color: red;">{{LGDValue}}</span>
        </div>
        <div class="form-group">
          <span>LGD级别</span> ：<span style="color: red;">{{LGDLevel}}</span>
        </div>
        <div class="form-group">
          <span>债券基础评级</span> ：<span style="color: red;">{{companyBasisRating}}</span>
        </div>
        <div class="form-group">
          <span>担保人有效评级</span> ：<span style="color: red;">{{guaranterRating}}</span>
        </div>
        <div class="form-group">
          <span>最终评级</span> ：<span style="color: red;">{{finallyRating}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      companyNum: 1, // 发行人信息
      pledgeNum: 0, // 抵质押品数量
      guarantorNum: 0, // 担保人数量

      companyScale: 1, // 发行人评级
      guarantorScale: 1, // 担保人评级
      scaleList: null, // 评级映射表

      companyList: [{
        companyNatureRatio: 0,
        industaryRatio: 0,
        creditRegionRatio: 0
      }], // 发行人列表
      pledgeList: [], // 抵质押列表
      warrantorList: [], // 担保人列表

      bondBalance: null, // 债券余额

      bondType: null, // 债项类型
      bondTypeRatio: null,

      companyNature: null, // 股权结构
      industary: null, // 发行人行业
      creditRegion: null, // 信用环境

      warrantorType: null, // 担保人类型
      warrantyStrength: null, // 担保强度
      guaranteeType: null, // 担保类型

      pledgeType: null, // 抵质押品类型
      pledgeControl: null, // 抵质押品控制力
      pledgeRegion: null, // 抵质押品执法环境
      pledgeDepend: null, // 抵质押物独立性

      LGDRules: null,

      // 计算结果：
      bondFeatureAdjustCoefficient: null, // 债项特征调整系数
      debtorFeatureAdjustCoefficient: null, // 债务人特征调整系数
      pledgeReleasePrice: null, // 抵质押品缓释价值
      warrantorReleasePrice: null, // 担保人缓释价值
      originalBasisRecoveryRate: null, // 原始的基础回收率
      basisRecoveryRate: null, // 基础回收率
      LGDValue: null, // LGD值
      LGDLevel: null, // LGD级别

      companyBasisRating: null, // 债券基础评级
      guaranterRating: null, // 担保人有效评级
      finallyRating: null, // 最终评级
    };
  },
  created() {
    axios({ url: "../../static/data/company/BOND_TYPE.json" }).then(res => {
      this.bondType = res.data;
      this.bondTypeRatio = res.data.options[0].ratio;
    });
    axios({ url: "../../static/data/company/CORP_NATURE.json" }).then(res => {
      this.companyNature = res.data;
      this.companyList[0].companyNatureRatio = res.data.options[0].ratio;
    });
    axios({ url: "../../static/data/company/INDUSTRY.json" }).then(res => {
      this.industary = res.data;
      this.companyList[0].industaryRatio = res.data.options[0].ratio;
    });
    axios({ url: "../../static/data/company/CREDIT_REGION.json" }).then(
      res => {
        this.creditRegion = res.data;
        this.companyList[0].creditRegionRatio = res.data.options[0].ratio;
      }
    );
    axios({ url: "../../static/data/guarantor/WARRANTOR_TYPE.json" }).then(
      res => {
        this.warrantorType = res.data;
      }
    );
    axios({ url: "../../static/data/guarantor/WARRANTY_STRENGTH.json" }).then(
      res => {
        this.warrantyStrength = res.data;
      }
    );
    axios({ url: "../../static/data/guarantor/GUARANTEE_TYPE.json" }).then(
      res => {
        this.guaranteeType = res.data;
      }
    );
    axios({ url: "../../static/data/pledge/PLEDGE_TYPE.json" }).then(res => {
      this.pledgeType = res.data;
    });
    axios({ url: "../../static/data/pledge/PLEDGE_CONTROL.json" }).then(
      res => {
        this.pledgeControl = res.data;
      }
    );
    axios({ url: "../../static/data/pledge/PLEDGE_REGION.json" }).then(
      res => {
        this.pledgeRegion = res.data;
      }
    );
    axios({ url: "../../static/data/pledge/PLEDGE_DEPEND.json" }).then(
      res => {
        this.pledgeDepend = res.data;
      }
    );
    axios({ url: "../../static/data/LGDRules.json" }).then(
      res => {
        this.LGDRules = res.data;
      }
    );
    axios({ url: "../../static/data/scales.json" }).then(
      res => {
        this.scaleList = res.data;
      }
    );
  },
  methods: {
    // 获取场景
    modelChange() {
      this.bondFeatureAdjustCoefficient = null;
      this.debtorFeatureAdjustCoefficient = null;
      this.pledgeReleasePrice = null;
      this.warrantorReleasePrice = null;
      this.originalBasisRecoveryRate = null;
      this.basisRecoveryRate = null;
      this.LGDValue = null;
      this.LGDLevel = null;

      this.companyList = [];
      this.pledgeList = [];
      this.warrantorList = [];
      // 发行人
      for(var i = 0; i < this.companyNum; i++) {
        var companyObj = {
          companyNatureRatio: this.companyNature.options[0].ratio,
          industaryRatio: this.industary.options[0].ratio,
          creditRegionRatio: this.creditRegion.options[0].ratio,
          companyPd: null
        }
        this.companyList.push(companyObj);
      }
      for(var i = 0; i < this.pledgeNum; i++) {
        var pledgeObj = {
          pledgePrice: null,
          pledgeDependRatio: this.pledgeDepend.options[0].ratio,
          pledgeRegionRatio: this.pledgeRegion.options[0].ratio,
          pledgeControlRatio: this.pledgeControl.options[0].ratio,
          pledgeTypeRatio: this.pledgeType.options[0].ratio
        }
        this.pledgeList.push(pledgeObj);
      }
      for(var i = 0; i < this.guarantorNum; i++) {
        var warrantorObj = {
          warrantorPrice: null,
          guaranteeTypeRatio: this.guaranteeType.options[0].ratio,
          warrantorTypeRatio: this.warrantorType.options[0].ratio,
          warrantyStrengthRatio: this.warrantyStrength.options[0].ratio,
          guarantorPd: null // 担保人违约率
        }
        this.warrantorList.push(warrantorObj);
      }
    },
    getResult() {
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
      this.bondFeatureAdjustCoefficient = this.bondTypeRatio;
    },
    // 债务人特征调整系数=股权结构*发行人行业*信用环境
    getDebtorFeatureAdjustCoefficient() {
      var temp = 0;
      for(var i = 0; i< this.companyNum; i++) {
        var item = this.companyList[i];
        temp += item.companyNatureRatio * item.industaryRatio * item.creditRegionRatio;
      }
      this.debtorFeatureAdjustCoefficient = temp / this.companyNum;
    },
    // 抵质押品缓释价值=抵质押品价值*抵质押品类型*抵质押品控制力*抵质押品执法环境
    getPledgeReleasePrice() {
      for(var i = 0;i < this.pledgeList.length; i++) {
        var item = this.pledgeList[i];
        this.pledgeReleasePrice += item.pledgePrice * item.pledgeTypeRatio * item.pledgeControlRatio * item.pledgeRegionRatio;
      }
    },
    // 担保人缓释价值=担保人类型*担保强度*担保价值*担保类型
    getWarrantorReleasePrice() {
      for(var i = 0;i < this.warrantorList.length; i++) {
        var item = this.warrantorList[i];
        this.warrantorReleasePrice += item.warrantorTypeRatio * item.warrantyStrengthRatio * item.warrantorPrice * item.guaranteeTypeRatio;
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
      for(var i = 0; i < this.LGDRules.length; i++) {
        var item = this.LGDRules[i];
        if (this.LGDValue > item.lowBound && this.LGDValue <= item.upperBound) {
          this.LGDLevel = item.level;
          break;
        }
      }
    },
    // 债券基础评级
    getCompanyBasisRating() {
      var LGDObj = this.LGDRules.filter(it => {
          return it.level===this.LGDLevel
        })[0];
      if (this.companyNum > 1) {
        // 平均违约率
        var averagePd = 0;
        this.companyList.forEach(it => {
          averagePd += it.companyPd * 1000;
        })
        averagePd = averagePd / this.companyList.length / 100000;
        this.companyScale = this.scaleList.filter(it => { // 发行人评级
          return averagePd > it.minValue && averagePd <= it.maxValue;
        })[0].id;
      }
      var temp = this.companyScale - LGDObj.adjust;
      if (temp < 1) {
        temp = 1;
      }
      this.companyBasisRating = this.scaleList.filter(it => {
        return it.id === temp
      })[0].rating;
    },
    // 担保人有效评级
    getGuaranterRating() {
      var LGDObj = this.LGDRules.filter(it => {
        return it.level===this.LGDLevel
      })[0];

      if (this.guarantorNum > 1) {
        // 平均违约率
        var averagePd = 0;
        this.warrantorList.forEach(it => {
          averagePd += it.guarantorPd * 1000;
        })
        averagePd = averagePd / this.warrantorList.length / 100000;
        this.guarantorScale = this.scaleList.filter(it => { // 发行人评级
          return averagePd > it.minValue && averagePd <= it.maxValue;
        })[0].id;
      }
      this.guaranterRating = this.scaleList.filter(it => {
        return it.id === this.guarantorScale
      })[0].rating;
    },
    // 最终评级
    getFinallyRating() {
      var companyScale = this.scaleList.filter(it => {
        return it.rating == this.companyBasisRating
      })[0];
      var guaranterScale = this.scaleList.filter(it => {
        return it.rating == this.guaranterRating
      })[0];
      this.finallyRating = companyScale.id < guaranterScale.id ? companyScale.rating : guaranterScale.rating;
    }
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a {
  color: #42b983;
}
.form-group {
  padding: 10px 0;
}
.left {
  float: left;
  width: 65%;
}
.btn {
  width: 150px;
  height: 40px;
  border: 1px solid #ccc;
  border-radius: 3px;
  text-align: center;
  line-height: 40px;
  cursor: pointer;
  background: #1875c7;
  color: #fff;
}
.result {
  width: 450px;
  position: fixed;
  right: 0;
}
.notice {
  margin-left: 10px;
  color: blue;
}
</style>
