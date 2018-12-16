<template>
  <div>
    <div class="bindingRect">
      <x-input title="客户姓名" type="text" v-model="custName" placeholder="请输入客户姓名"></x-input>
      <popup-radio title="卡号类型" :options="cardTypeList" v-model="cardType"></popup-radio>
      <select class="selectCardType" v-model="cardType">
        <option v-for="selectCardType  in cardTypeList"  :value="selectCardType.key" >{{selectCardType.value}}</option>
      </select>

      <x-input title="绑定卡号" type="text" v-model="cardNumber" placeholder="请输入智能卡号/客户编号/宽带账号"></x-input>
      <popup-radio title="所在区域" :options="areaList" v-model="area"></popup-radio>
      <select class="selectArea" v-model="area">
        <option v-for="selectArea  in areaList"  :value="selectArea.key" >{{selectArea.value}}</option>
      </select>
      <section>
        <search @selectFunc="selectFunc" :searchList="areaList" :searchKey="area" :searchValue="areaValue"></search>
      </section>
    </div>
    <divider>您可以点击“人工”菜单，由我们帮您绑定</divider>
    <box gap="10px 10px">
      <x-button :gradients="['#f7b45d','#e40e2a']" @click.native="submit">绑定</x-button>
    </box>
    <div>
      <x-dialog v-model="showToast" class="dialog-demo">
        <div style="padding:15px;">
          <div>提示</div>
          {{dialogText}}
        </div>
        <div>
          <x-button :gradients="['#f7b45d','#e40e2a']" @click.native="clickToast">确定</x-button>
        </div>
      </x-dialog>
    </div>
  </div>
</template>

<script>
  import {Group, XInput, Box, XButton, XDialog, PopupRadio, Divider} from 'vux'
  import store from '@/vuex/store'
  import Search from '../components/Search'
  let Base64 = require('js-base64').Base64
  export default {
    components: {
      Group,
      Box,
      Divider,
      XButton,
      XDialog,
      PopupRadio,
      XInput,
      Search
    },
    mounted: function () {
      if (window.location.href.indexOf('param') > -1) {
        let params = JSON.parse(Base64.fromBase64(decodeURIComponent(window.location.href.split('param=')[1])))
        sessionStorage.setItem('openId', params.openId)
        this.area = params.area
        this.custName = params.custName
        this.cardNumber = params.code
        this.cardType = params.type
      }
      window.addEventListener('popstate', function () {
        if (window.location.href.indexOf('binding')) {
          window.WeixinJSBridge.call('closeWindow')
        }
      }, false)
      this.openId = sessionStorage.getItem('openId')
//    this.openId = 'oW0Dds3MA0jdmTPKD8LJzepV7VLU'
      let self = this
      this.http({
        method: 'get',
        url: store.state.apiUrl + '/mobileArea.action?openId=' + self.openId
      }).then(function (response) {
        if (response.data.status == 'success') {
          self.area = response.data.result
        }
      })
    },
    methods: {
      selectFunc (value) {
        this.area = value.key
        this.areaValue = value.value
        console.log(this.area)
      },
      clickToast: function () {
        if (this.dialogText == '绑定成功') {
          this.getCust()
        } else {
          this.showToast = false
        }
      },
      submit: function () {
        let self = this
        if (self.custName == '' || self.custPhone == '') {
          this.dialogText = '请填写完整信息后再进行提交'
          this.showToast = true
        } else {
          this.$vux.loading.show({
            text: '加载中'
          })
          this.http({
            method: 'get',
            url: store.state.apiUrl + '/mobileValidCard.action?custName=' + self.custName + '&smsId=' + self.cardNumber + '&openId=' + self.openId + '&area=' + self.area + '&cardType=' + self.cardType
          }).then(function (response) {
            if (response.data.status == 'SUCCESS') {
              self.$vux.loading.hide()
              self.dialogText = '绑定成功'
              self.showToast = true
              store.state.msgId = self.cardNumber
              sessionStorage.setItem('msg', 'SUCCESS')
              if (window.location.href.indexOf('param') > -1) {
                window.WeixinJSBridge.call('closeWindow')
              }
            } else {
              self.$vux.loading.hide()
              self.dialogText = response.data.message
              self.showToast = true
            }
          })
        }
      },
      getCustInfoBySmsId: function (smsId, city) {
        let self = this
        self.http({
          method: 'post',
          url: store.state.apiUrl + '/getCustInfoBySmsId.action?smsId=' + smsId + '&city=' + city
        }).then(function (response) {
          store.state.userInfo = response.data.split('&&')
          self.$router.go(-1)
        })
      },
      getCust: function () {
        let self = this
        self.http({
          method: 'post',
          url: store.state.apiUrl + '/mobileValidBD.action?openId=' + self.openId
        }).then(function (response) {
          if (response.data.msg == 'SUCCESS') {
            sessionStorage.setItem('userList', JSON.stringify(response.data.result))
            if (sessionStorage.getItem('smsid') == '' || sessionStorage.getItem('smsid') == undefined || sessionStorage.getItem('smsid') == null) {
              sessionStorage.setItem('smsid', Base64.encode(response.data.result[0].smsId))
              sessionStorage.setItem('city', response.data.result[0].area)
              self.getCustInfoBySmsId(Base64.encode(response.data.result[0].smsId), response.data.result[0].area)
            } else {
              self.$vux.loading.hide()
              self.$router.go(-1)
            }
          }
        })
      }
    },
    data () {
      return {
        custName: '',
        cardNumber: '',
        dialogText: '',
        showToast: false,
        cardType: 'sms',
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
  .vux-popup-dialog {
    max-height: 40% !important;
  }
  .bindingRect div{
    font-size: 17px;
  }
  .bindingRect .weui-label{
    width: 5.5rem!important;
  }

  .bindingRect .vux-x-input{
    text-indent: 0rem;
  }
  .bindingRect .weui-cell{
    font-size: 16px;
    height: 1.5rem;
    border: 1px solid #572528;
    margin: 0.5rem;
  }
</style>
