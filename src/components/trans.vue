<template>

<div class="container trans">
<div class="alert alert-warning" role="alert">
EI电源变压器计算器v0.1-20190719 - <a href="mailto:kokonol@126.com">kokonol@126.com</a> 
</div>
  <div class="row">
    <div class="col-sm">
      <div class="card input-group mb-3 input-group-sm">
        <div class="card-header" id="headingOne">
          基本信息
        </div>
        <ul style="margin-top:10px">
          <li>
            <div class="input-group mb-3 input-group-sm">
              <div class="input-group-prepend">
                <span class="input-group-text" style="width:90px">铁芯类型</span>
              </div>
              <select name="public-choice" class="form-control" v-model="eiTypeSelected">                                        
                <option v-for="eiType in eiTypeList" :key='eiType.id' :value='eiType' > {{ eiType.name }} </option>                                    
            </select>
            </div>
          </li>
          <li>
            <div class="input-group mb-3 input-group-sm">
              <div class="input-group-prepend">
                <span class="input-group-text" style="width:90px">铁芯尺寸</span>
              </div>
              <select name="public-choice" class="form-control" v-model="eiModelSelected">                                        
                <option v-for="eiModel in eiModelList" :key='eiModel.id' :value="eiModel"> {{ eiModel.name }} </option>                                    
              </select>
            </div>
          </li>
          <li>
            <div class="input-group mb-3 input-group-sm">
              <div class="input-group-prepend">
                <span class="input-group-text" style="width:90px">叠厚</span>
              </div>
              <input type="text" class="form-control" v-model="thickness">
              <div class="input-group-append">
                <span class="input-group-text" style="width:40px">cm</span>
              </div>
            </div>
          </li>
          <li>
            <div class="input-group mb-3 input-group-sm">
              <div class="input-group-prepend">
                <span class="input-group-text" style="width:90px">磁感应强度(T)</span>
              </div>
              <input type="text" class="form-control" v-model="eiTypeSelected.magneticInduction">
              <div class="input-group-append">
                <span class="input-group-text" style="width:40px">T</span>
              </div>
            </div>
          </li>
          <li>
            <div class="input-group mb-3 input-group-sm">
              <div class="input-group-prepend">
                <span class="input-group-text" style="width:90px">电流密度</span>
              </div>
              <input type="text" class="form-control" v-model="currentDensity">
              <div class="input-group-append">
                <span class="input-group-text" style="width:40px">A</span>
              </div>
            </div>
          </li>
        </ul>
        <button class="btn btn-link btn-sm" @click="basicInfoShow = !basicInfoShow">更多信息</button>
        <transition name="basicInfo">
          <div class="card-body" v-if="basicInfoShow">
              <ul >
                <li>舌宽: {{ this.eiModelSelected.a }}cm </li>
                <li>初级每伏匝数：{{ tv1.toFixed(2) }}匝/伏 </li>
                <li>次级每伏匝数：{{ tv2.toFixed(2) }}匝/伏 </li>
                <li>有效截面积：{{ csa.toFixed(2) }} cm<sup>2</sup></li>
                <li>窗高: {{ this.eiModelSelected.a * 1.5 }}cm</li>
                <li>窗宽: {{ this.eiModelSelected.a * 0.5 }}cm</li>
                <li>窗口面积: {{ windowArea.toFixed(2) }}cm<sup>2</sup></li>
                <li>初级每匝长度: {{ longPerT1 }}cm</li>
                <li>次级每匝长度: {{ longPerT2 }}cm</li>
              </ul>
          </div>
        </transition>
      </div>
    </div>
  </div>

  <div class="row">
    <div class="col-sm">
      <div class="card" >
        <div class="card-header">
          初级
        </div>
        <div class="container">
          <div class="row" v-for="(primary, index) in primaryList" :key="index" :value="primary">
            <div class="col-10">
              <div class="row">
                <button class="btn btn-link btn-sm" @click="showPrimaryDetail(index)">
                  {{index+1}} ： {{primary.voltage}}V - {{primary.current}}A - {{(primary.voltage * tv1).toFixed(2)}}T - {{premaryDiameter.toFixed(2)}}mm
                </button>
              </div>
              <div class="row" v-if="primaryShowDetailIndex==index">
                <ul>
                  <li>电压：{{primary.voltage}}V</li>
                  <li>电流：{{primary.current}}A</li>
                  <li>匝数(理论值)：{{(primary.voltage * tv2).toFixed(2)}}T</li>
                  <li>线径(理论值)：{{premaryDiameter.toFixed(2)}}mm</li>
                  <li>铜阻(理论值)：{{premaryResistanceList[index].toFixed(2)}}Ω</li>
                </ul>
              </div>
            </div>
            <div class="col-2"><button class="btn btn-outline-secondary btn-sm" v-on:click="delPrimary(index)" >删除</button></div>
          </div>
          <div class="row">
            <div class="col-10">
              <div class="input-group mb-3 input-group-sm">
                <div class="input-group-append">
                  <span class="input-group-text">增加绕组</span>
                </div>
                <input v-model="newPrimaryCurrent" class="form-control">
                <div class="input-group-prepend">
                  <span class="input-group-text">A @ </span>
                </div>
                <input v-model="newPrimaryVotage" class="form-control" >
                <div class="input-group-append">
                  <span class="input-group-text">V</span>
                </div>
              </div>
            </div>
            <div class="col-2"><button class="btn btn-outline-success btn-sm" v-on:click="newPrimary" >增加</button></div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="row">
    <div class="col-sm">
      <div class="card" >
        <div class="card-header">
          次级
        </div>
        <div class="container">
          <div class="row" v-for="(secondary, index) in secondaryList" :key="index" :value="secondary">
            <div class="col-10">
              <div class="row">
                <button class="btn btn-link btn-sm" @click="showSecondaryDetail(index)">
                  {{index+1}} ： {{secondary.voltage}}V - {{secondary.current}}A - {{ (secondary.voltage * tv2).toFixed(2)}}T - {{secondaryDiameterList[index].toFixed(2)}}mm
                </button>
              </div>
              <div class="row" v-if="secondaryShowDetailIndex==index">
                <ul>
                  <li>电压：{{secondary.voltage}}V</li>
                  <li>电流：{{secondary.current}}A</li>
                  <li>匝数(理论值)：{{(secondary.voltage * tv2).toFixed(2)}}T</li>
                  <li>线径(理论值)：{{secondaryDiameterList[index].toFixed(2)}}mm</li>
                  <li>铜阻(理论值)：{{secondaryResistanceList[index].toFixed(2)}}Ω</li>
                </ul>
              </div>
            </div>
            <div class="col-2"><button class="btn btn-outline-secondary btn-sm" v-on:click="delSecondary(index)" >删除</button></div>
          </div>

          <div class="row">
            <div class="col-10">
              <div class="input-group mb-3 input-group-sm">
                <div class="input-group-append">
                  <span class="input-group-text">增加绕组</span>
                </div>
                <input v-model="newSecondaryCurrent" class="form-control">
                <div class="input-group-prepend">
                  <span class="input-group-text">A @ </span>
                </div>
                <input v-model="newSecondaryVotage" class="form-control" >
                <div class="input-group-append">
                  <span class="input-group-text">V</span>
                </div>
              </div>
            </div>
            <div class="col-2"><button class="btn btn-outline-success btn-sm" v-on:click="newSecondary" >增加</button></div>
          </div>
        </div>
      </div>
    </div>
  </div>

</div>


</template>

<script>
var ei_type = {
  z9:{
    id:'z9',
    name:'Z9(1.3T)',
    magneticInduction:'1.3'
  },
  z10:{
    id:'z10',
    name:'Z10(1.2T)',
    magneticInduction:'1.2'
  },
  z11_0:{
    id:'z11-0',
    name:'Z11全新(1.2T)',
    magneticInduction:'1.2'
  },
  z11_1:{
    id:'z11-1',
    name:'Z11(1.1T)',
    magneticInduction:'1.1'
  },
  z11_2:{
    id:'z11-2',
    name:'Z11拆机(1.0T)',
    magneticInduction:'1.0'
  },
  h18:{
    id:'h18',
    name:'H18(0.8T)',
    magneticInduction:'0.8'
  },
  h14:{
    id:'h14',
    name:'H14(0.7T)',
    magneticInduction:'0.7'
  },
  h12:{
    id:'h12',
    name:'H12(0.6T)',
    magneticInduction:'0.6'
  }
}
var ei_model = {
  ei48:{
    id:'48',
    name:"EI-48",
    a:1.6,
    packingFactor:0.8
  },
  ei57:{
    id:'57',
    name:"EI-57",
    a:1.9,
    packingFactor:0.81
  },
  ei66:{
    id:'66',
    name:"EI-66",
    a:2.2,
    packingFactor:0.83
  },
  ei76:{
    id:'76',
    name:"EI-76",
    a:2.54,
    packingFactor:0.84
  },
  ei86:{
    id:'86',
    name:"EI-86",
    a:2.86,
    packingFactor:0.85
  },
  ei96:{
    id:'96',
    name:"EI-96",
    a:3.2,
    packingFactor:0.87
  },
  ei105:{
    id:'105',
    name:"EI-105",
    a:3.5,
    packingFactor:0.88
  },
  ei114:{
    id:'114',
    name:"EI-114",
    a:3.8,
    packingFactor:0.89
  },
  ei133:{
    id:'133',
    name:"EI-133",
    a:4.44,
    packingFactor:0.9
  }
};
export default {
  name: 'Trans',
  data: function(){ 
    return {
      basicInfoShow: false,
      eiModelList: ei_model,
      eiModelSelected:'',
      eiTypeList: ei_type,
      eiTypeSelected:'',
      thickness: '',
      currentDensity:2.5,
      primaryList: [
        {voltage:220, current:0.45},
        {voltage:10, current:0.45}
      ],
      newPrimaryCurrent:'',
      newPrimaryVotage:'',
      secondaryList: [
        {voltage:300, current:0.1},
        {voltage:6.3, current:0.45}
      ],
      newSecondaryCurrent:'',
      newSecondaryVotage:'',
      primaryShowDetailIndex:-1,
      secondaryShowDetailIndex:-1
    } 
  },
  computed: {
    fullName: function () {
      if(!this.eiModelSelected){
        return "please input INFO"
      }
      return this.eiModelSelected.a + " * " + this.thickness + "cm";
    },
    csa: function () {
      return this.eiModelSelected.a*this.thickness*this.eiModelSelected.packingFactor;
    },
    tv1: function () {
      return 45/this.csa/this.eiTypeSelected.magneticInduction;
    },
    longPerT1: function() {
      return this.eiModelSelected.a*3+this.thickness*2;
    },
    longPerT2: function() {
      return this.eiModelSelected.a*6+this.thickness*2;
    },
    windowArea: function() {
      return this.eiModelSelected.a * this.eiModelSelected.a * 0.75;
    },
    primaryTurnsList: function() {
      var turns = [];
      this.primaryList.forEach(primaryItem => {
        turns.push(primaryItem.voltage * this.tv1);
      });
      return turns;
    },
    secondaryTurnsList: function() {
      var turns = [];
      this.secondaryList.forEach(secondaryItem => {
        turns.push(secondaryItem.voltage * this.tv2);
      });
      return turns;
    },
    premaryDiameter: function() {
      var turnsCount = 0;
      this.primaryTurnsList.forEach(element => {
        turnsCount = turnsCount + element;
      });
      return 2*Math.sqrt(this.windowArea*100*0.38*0.5)/Math.sqrt((turnsCount)*3.14);
    },
    premaryResistanceList: function() {
      var resistances = [];
      this.primaryTurnsList.forEach(item => {
        resistances.push(item*this.longPerT1*0.000175/((this.premaryDiameter/2)*(this.premaryDiameter/2)*3.14));
      });
      return resistances;
    },
    secondaryResistanceList: function() {
      var resistances = [];
      var i=0;
      this.secondaryTurnsList.forEach(item => {
        resistances.push(item*this.longPerT2*0.000175/((this.secondaryDiameterList[i]/2)*(this.secondaryDiameterList[i]/2)*3.14));
        i++;
      });
      return resistances;
    },
    tv2: function() {
      var premaryResistanceCount = 0;
      this.premaryResistanceList.forEach(premaryResistance => {
        premaryResistanceCount += premaryResistance;
      });
      var premaryVoltageCount = 0;
      this.primaryList.forEach(primary => {
        premaryVoltageCount += primary.voltage;
      });
      // TV1/(1-((1+(次级匝长/初级匝长))*(初级铜阻*初级电流)/(初级电压总数)))
      return this.tv1/(1-((1+(this.longPerT2/this.longPerT1))*(premaryResistanceCount*this.primaryList[0].current)/(premaryVoltageCount)));
    },
    secondaryDiameterList: function() {
      var secondaryDiameterList = [];
      this.secondaryList.forEach(secondaryItem => {
        // =2*SQRT((dianliu/dianliumidu)/3.14)
        secondaryDiameterList.push(2*Math.sqrt((secondaryItem.current/this.currentDensity)/3.14))
      });
      return secondaryDiameterList;
    }
  },
  methods: {
    newPrimary:function(){
      this.primaryList.push({voltage:this.newPrimaryVotage, current:this.newPrimaryCurrent});
    },
    delPrimary:function(index){
      this.primaryList.splice(index,1);
    },
    newSecondary:function(){
      this.secondaryList.push({voltage:this.newSecondaryVotage, current:this.newSecondaryCurrent});
    },
    delSecondary:function(index){
      this.secondaryList.splice(index,1);
    },
    showPrimaryDetail: function(index){
      if(this.primaryShowDetailIndex == index){
        this.primaryShowDetailIndex = -1;
      } else {
        this.primaryShowDetailIndex = index;
      }
    },
    showSecondaryDetail: function(index){
      if(this.secondaryShowDetailIndex == index){
        this.secondaryShowDetailIndex = -1;
      } else {
        this.secondaryShowDetailIndex = index;
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  /* display: inline-block; */
  margin: 0 10px;
}
a {
  color: #42b983;
}

.basicInfo-enter-active {
  animation: basicInfo-in .5s;
}
.basicInfo-leave-active {
  animation: basicInfo-in .5s reverse;
}
@keyframes basicInfo-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
.lable {width: 100px;display:block;}
.input {width: 100px;display:block;}
</style>
