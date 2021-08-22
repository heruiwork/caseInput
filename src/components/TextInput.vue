<template>
  <div class="text-input-pannel">
    <div class="textarea" ref="textarea" contenteditable="plaintext-only" @input="onChange"></div>
    <!-- <textarea 
      v-model="inputVal"
      ref="textarea"
      class="textarea" 
      placeholder="请输入内容"
      @keyup="keyPress">
    </textarea> -->
    <!-- <div ref="text_copy" class="textarea" style="visibility:hidden; position:absolute; left:0; top:0"></div> -->
    <SearchList
      v-show="showList"
      :infoList="searchList"
      :position="position"
      @confirm="confirm">
    </SearchList>
  </div>
</template>

<script>
import SearchList from './_SearchList.vue';
export default {
  name: 'TextInput',
  props: {
    totalList: {
      type: Array,
      default: () => {
        return []
      }
    }
  },
  components: {
    SearchList
  },
  mounted() {
    // this.$refs.textarea.addEventListener('input', (e) => {
    //   console.log(e)
    // })
  },
  data() {
    return {
      showList: false,
      inputVal: '',
      position: {
        left: 20,
        top: 40
      },
      searchList: [],
      filterFlag: false,
      preVal: null,
      atIndex: 0,
      filterTimer: 0,
      filterKey: ''
    }
  },
  methods: {
    // keyPress(e) {
    //   if(e.key === '@') {
    //     // 根据最新的一个@符号进行搜索
    //     this.searchList = this.totalList
    //     this.position = this.getPosition()
    //     this.createFilter()
    //     this.showList = true
    //   } else if (e.key === 'Backspace') {
    //     if (this.filterFlag && this.inputVal.length < this.atIndex) {
    //       // 正在搜索，但是删除了@
    //       this.showList = false
    //       this.searchList = []
    //       clearTimeout(this.filterTimer)
    //     }
    //   }
    // },
    onChange(e) {
      if (e.inputType === 'insertText') {
        if (e.data === '@') {
          this.getPosition()
          this.createFilter()
        } else if (this.filterFlag) {
          this.filterKey += e.data
        }
      } else if (e.inputType === 'deleteContentBackward') {
        if (this.filterFlag && this.filterKey === '') {
          this.closeFilter()
        } else if (this.filterFlag && this.filterKey !== '') {
          this.filterKey = this.filterKey.substr(0, this.filterKey.length - 1)
        }
      }
    },
    getPosition() {
      
      // let {offsetLeft, offsetTop} = document.getElementById('anchor')
      // if (offsetTop > 400) {
      //   offsetTop = offsetTop - 400
      // }
      // return {left: offsetLeft, top: offsetTop + 20};
    },
    getCaret() {
      // let target = this.$refs.textarea
      // if (target.selectionStart) {
      //     return target.selectionStart;
      // } else if (!document.selection) {
      //     return 0;
      // }

      // var c = "\001",
      //     sel = document.selection.createRange(),
      //     dul = sel.duplicate(),
      //     len = 0;

      // dul.moveToElementText(node);
      // sel.text = c;
      // len = dul.text.indexOf(c);
      // sel.moveStart('character', -1);
      // sel.text = "";
      // console.log(len)
      // return len;
    },
    createFilter() {
      this.filterFlag = true
      this.filterKey = ''
      this.preVal = null
      // this.atIndex = this.preVal.length
      if (this.filterTimer) {
        clearTimeout(this.filterTimer)
      }
      const handle = () => {
        // let cur = this.inputVal
        // let msg = this.inputVal.substring(this.preVal.length, cur.length)
        // if (this.filterKey === msg) {
        //   // 查询值没有发生变化
        //   return this.filterTimer = setTimeout(handle, 500)
        // }
        // this.filterKey = msg
        if (this.preVal !== this.filterKey) {
          this.preVal = this.filterKey
          if (this.filterKey) {
            this.searchList = this.totalList.filter(person => person.name.includes(this.filterKey))
          } else if (this.searchList.length != this.totalList.length) {
            // 查询值变成了空字符串，说明是查询全部
            this.searchList = this.totalList
          }
        }
        this.showList = true
        return this.filterTimer = setTimeout(handle, 500)
      }
      handle()
    },
    closeFilter() {
      this.filterFlag = false
      clearTimeout(this.filterTimer)
      this.showList = false
    },
    confirm(obj) {
      // this.inputVal = this.inputVal.substr(0, this.atIndex) + obj.name + ' '
      this.$refs.textarea.innerHTML += obj.name
      this.$refs.textarea.focus()
    }
  }
}
</script>

<style scoped>
.text-input-pannel {
  position: relative;
  width: 400px;
  height: 400px;
  margin: 0 auto;
  border: 1px solid #0000ff57;
  border-radius: 5px;
}
.textarea {
  position: relative;
  width: 100%;
  height: 100%;
  padding: 20px;
  border: none;
  word-break: break-all;
  overflow-x: hidden;
  outline: none;
  font-size: 14px;
  overflow-y: auto;
  resize: none;
  column-count: initial !important;
  white-space: pre-wrap;
  font-family: 'Courier New', Courier, monospace;
}
</style>