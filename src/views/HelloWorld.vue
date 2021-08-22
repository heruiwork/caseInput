<template>
  <div class="demo-content">
    <TextInput :totalList="totalList"></TextInput>
  </div>
</template>

<script>
import TextInput from '../components/TextInput.vue'
export default {
  name: 'HelloWorld',
  components:{
    TextInput
  },
  data() {
    return {
      totalList: [
        {img: '', name: '路飞1'},
        {img: '', name: '索隆1'},
        {img: '', name: '鸣人1'},
        {img: '', name: '佐助1'},
        {img: '', name: '柯南1'}, 
        {img: '', name: '张三1'}, 
        {img: '', name: '李四1'}, 
        {img: '', name: '娜美1'},
        {img: '', name: '路飞2'},
        {img: '', name: '索隆2'},
        {img: '', name: '鸣人2'},
        {img: '', name: '佐助2'},
        {img: '', name: '柯南2'}, 
        {img: '', name: '张三2'}, 
        {img: '', name: '李四2'}, 
        {img: '', name: '娜美2'},
        {img: '', name: '路飞3'},
        {img: '', name: '索隆3'},
        {img: '', name: '鸣人3'},
        {img: '', name: '佐助3'},
        {img: '', name: '柯南3'}, 
        {img: '', name: '张三3'}, 
        {img: '', name: '李四3'}, 
        {img: '', name: '娜美3'},
        {img: '', name: '路飞4'},
        {img: '', name: '索隆4'},
        {img: '', name: '鸣人4'},
        {img: '', name: '佐助4'},
        {img: '', name: '柯南4'}, 
        {img: '', name: '张三4'}, 
        {img: '', name: '李四4'}, 
        {img: '', name: '娜美4'},
        {img: '', name: '路飞5'},
        {img: '', name: '索隆5'},
        {img: '', name: '鸣人5'},
        {img: '', name: '佐助5'},
        {img: '', name: '柯南5'}, 
        {img: '', name: '张三5'}, 
        {img: '', name: '李四5'}, 
        {img: '', name: '娜美5'},
        {img: '', name: '路飞6'},
        {img: '', name: '索隆6'},
        {img: '', name: '鸣人6'},
        {img: '', name: '佐助6'},
        {img: '', name: '柯南6'}, 
        {img: '', name: '张三6'}, 
        {img: '', name: '李四6'}, 
        {img: '', name: '娜美6'},
        {img: '', name: '路飞7'},
        {img: '', name: '索隆7'},
        {img: '', name: '鸣人7'},
        {img: '', name: '佐助7'},
        {img: '', name: '柯南7'}, 
        {img: '', name: '张三7'}, 
        {img: '', name: '李四7'}, 
        {img: '', name: '娜美7'}
      ]
    }
  }
}
</script>

<style scoped>
</style>
