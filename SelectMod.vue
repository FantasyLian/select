<template>
  <div class="selectMod">
    <div v-model="selectKey" @click="changeToggle" class="showValue">{{selectValue}}</div>
    <ul v-if="showUL" class="selectList">
      <li v-for="item in search" v-model="item.key" @click="chooseItem(item.key,item.value)" :class="item.key==selectKey?'selectedOne':''">{{item.value}}</li>
    </ul>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        //由于vue2是单向数据流，子组件不可改变父组件的数据，定义新的变量可以方便操作数据，不过这个组件本身不需要改变来自父组件的数据，所以可以不定义
        search:this.searchList,
        //显示所选的元素
        selectValue:'',
        selectKey:'',
        showUL:false
      }
    },
    props: {
      searchList: Array,
    },
    mounted () {
      this.selectValue=this.search[0].value
      this.selectKey=this.search[0].key

    },
    methods: {
      //点击div时将ul隐藏或显示
      changeToggle(){
        let self=this
        self.showUL=!self.showUL
      },
      //处理点击元素,并传值给父组件
      chooseItem(chooseKey,chooseValue){
        let self=this
        self.showUL=false
        self.selectKey=chooseKey
        self.selectValue=chooseValue
        self.$emit('chooseItem',self.selectKey,self.selectValue);
      }

    }
  }
</script>

<style scoped>
  .selectMod{
    box-sizing: border-box;
  }
  .showValue{
    width: 7rem;
    height: 2rem;
    border: 0.5px solid;
    border-radius: 7px;
    line-height: 2rem;
    text-align: center;
  }
  .selectList{
    margin: 0;
    padding: 0px;
    width: 7rem;
    border: 0.5px solid;
    overflow: scroll;
    height: 9rem;
    border-radius: 7px;
  }
  .selectList li{
    height: 1.5rem;
    line-height: 1.5rem;
    text-align: center;
  }
  .selectedOne{
    background-color: red;
    color: antiquewhite;
  }
</style>
