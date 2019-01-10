<template>
  <div class="bindingRect">
    <select-mod :searchList="areaList" @chooseItem="chooseArea"></select-mod>
    <select-mod :searchList="cardTypeList" @chooseItem="chooseCardType"></select-mod>
  </div>
</template>

<script>
  import store from '@/vuex/store'
  import SelectMod from './SelectMod.vue'
  let Base64 = require('js-base64').Base64
  export default {
    components: {
      SelectMod
    },
    mounted: function () {
    },
    methods: {
      //接收并处理子组件的值
      chooseArea(selectKey,selectValue){
        let self=this
        self.area=selectKey
        self.areaValue=selectValue
        console.log(self.area)
        console.log(self.areaValue)
      },
      chooseCardType(selectKey,selectValue){
        let self=this
        self.cardType=selectKey
        self.cardTypeValue=selectValue
        console.log(self.cardType)
        console.log(self.cardTypeValue)
      }
    },
    data () {
      return {
        cardType: 'sms',
        cardTypeValue: '智能卡号',
        cardTypeList: [
          {
            key: 'sms',
            value: '智能卡号'
          }, {
            key: 'client',
            value: '客户编号'
          }, {
            key: 'broadband',
            value: '宽带账号'
          }
        ],
        area: 'JS',
        areaValue: '济南',
        areaList: [
          {
            key: 'JS',
            value: '济南'
          }, {
            key: 'QD',
            value: '青岛'
          }, {
            key: 'ZB',
            value: '淄博'
          }, {
            key: 'DY',
            value: '东营'
          }, {
            key: 'YT',
            value: '烟台'
          }, {
            key: 'WF',
            value: '潍坊'
          }, {
            key: 'JN',
            value: '济宁'
          }, {
            key: 'TA',
            value: '泰安'
          }, {
            key: 'WH',
            value: '威海'
          }, {
            key: 'RZ',
            value: '日照'
          }, {
            key: 'BZ',
            value: '滨州'
          }, {
            key: 'DZ',
            value: '德州'
          }, {
            key: 'LY',
            value: '临沂'
          }, {
            key: 'TC',
            value: '聊城'
          }, {
            key: 'HZ',
            value: '菏泽'
          }, {
            key: 'LW',
            value: '莱芜'
          }, {
            key: 'LC',
            value: '历城'
          }
        ]
      }
    }
  }
</script>
<style>
  .bindingRect{
    box-sizing: border-box;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
  }
</style>
