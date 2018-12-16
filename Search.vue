<template>
  <div class="searchZJ">
    <div class="f-search">
      <div class="f-searchIn" :class="{searchInFous: this.fousFlag}"  @click="searchDown" v-model="searchKey">{{this.searchValue}}<span :class="{searchActive: this.searchFlag}"></span></div>
      <div class="f-searchXl" v-if="this.dataHas" :style="{height:this.searchFous, border:this.searchBorder}">
        <div v-for="item in searchList" @click="choseValue(item.key,item.value)">{{item.value}}</div>
      </div>
      <div class="f-searchXl" v-else >
        <div>暂无数据</div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        data: [],
        dataHas: true,
        searchFlag: false,
        searchFous: '0',
        fousFlag: false,
        searchValue: '',
        searchKey: '',
        searchBorder: 'none'
      }
    },
    props: {
      searchList: Array,
      searchValue: String,
      searchKey: String
    },
    mounted () {
      this.data = this.searchList
      console.log(this.searchKey)

      console.log(this.searchValue)
    },
    methods: {
      searchDown () {
        this.searchFlag === false ? this.searchFlag = true : this.searchFlag = false
        this.searchFous === '0' ? this.searchFous = 'auto' : this.searchFous = '0'
        this.searchBorder === 'none' ? this.searchBorder = '1px solid #D9D9D9' : this.searchBorder = 'none'
        this.fousFlag === false ? this.fousFlag = true : this.fousFlag = false
        console.log(this.searchFlag)
      },
      choseValue (key, value) {
        this.searchValue = value
        this.searchKey = key
        this.searchDown()
        this.$emit('selectFunc', {
          key: key,
          value: value
        })
      }
    }
  }
</script>

<style>
  .f-search{
    width: 250px;
    height: auto;
    position: relative;
    margin-left: 20px;
    box-sizing: border-box;
  }
  .f-searchIn{
    width: 250px;
    height: 35px;
    line-height: 35px;
    font-size: 0.95rem;
    border-radius: 5px;
    overflow: hidden;
    position: relative;
    background-color: white;
    box-shadow: none;
    box-sizing: border-box;
    color: #000000;
    padding-left: 10px;
    border: 1px solid #A3A3A3;
  }
  .searchInFous{
    border: 1px solid #57C4F6;
    box-shadow: 0px 0px 5px #57C4F6;
  }
  .f-searchIn > span{
    display: block;
    width: 28px;
    height: 28px;
    background-size: 100% 100%;
    background-repeat: no-repeat;
    background-position: 0px -13px;
    position: absolute;
    top: 10px;
    right: 5px;
  }
  .f-searchIn .searchActive{
    background-position: 0px 12px;
    top: -2px;
  }
  .f-search .f-searchXl{
    position: absolute;
    width: 100%;
    height: auto;
    max-height: 220px;
    top: 41px;
    left: -1px;
    border-radius: 5px;
    /*border: 1px solid #D9D9D9;*/
    background-color: white;
    overflow-x: hidden;
    overflow-y: scroll;
  }
  .f-search .f-searchXl > div{
    height: 35px;
    line-height: 38px;
    color: #000000;
    padding-left: 25px;
    font-size: 0.92rem;
  }
  .f-search .f-searchXl > div:hover{
    background-color: #D5F1FD;
  }
</style>
