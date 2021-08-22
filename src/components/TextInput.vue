<template>
  <div class="text-input-pannel">
    <div class="textarea" ref="textarea" contenteditable="plaintext-only" @input="onChange" @click="resetRange"></div>
    <!-- <textarea 
      v-model="inputVal"
      ref="textarea"
      class="textarea" 
      placeholder="请输入内容"
      @keyup="keyPress">
    </textarea> -->
    <!-- <div ref="text_copy" class="textarea" style="visibility:hidden; position:absolute; left:0; top:0"></div> -->
    <SearchList
      :isShow="showSearchListBlock"
      :infoList="searchList"
      :position="position"
      @confirm="confirm">
    </SearchList>
  </div>
</template>

<script>
import SearchList from './_SearchList.vue';

let demoPosition = null

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
    demoPosition = this.$refs.textarea.getBoundingClientRect()
  },
  watch: {
    lastEditRange() {
      console.log(this.lastEditRange.endOffset)
    }
  },
  data() {
    return {
      showSearchListBlock: false,
      inputVal: '',
      position: {
        left: 20,
        top: 40
      },
      searchList: [],
      lastEditRange: null,
      filterFlag: false,
      preVal: null,
      atIndex: 0,
      filterTimer: 0,
      filterKey: ''
    }
  },
  methods: {
    // 点击设置光标位置
    resetRange() {
      const range = getSelection().getRangeAt(0)
      // 如果正在过滤，但是用户点击了其他文本区域
      if (this.filterFlag && range.startOffset != this.lastEditRange.startOffset) {
        this.closeFilter()
      }
      this.lastEditRange = range
    },
    // 监听输入
    onChange(e) {
      if (e.inputType === 'insertText') {
        if (e.data === '@') {
          this.lastEditRange = getSelection().getRangeAt(0)
          this.position = this.getPosition()
          this.createFilter()
        } else if (this.filterFlag) {
          this.filterKey += e.data
        }
      } else if (e.inputType === 'deleteContentBackward') {
        if (this.filterFlag && this.filterKey !== null) {
          if (this.filterKey === '') {
            this.filterKey = null
            this.closeFilter()
          } else {
            this.filterKey = this.filterKey.substr(0, this.filterKey.length - 1)
          }
        }
      } else if (e.inputType === 'insertCompositionText') {
        // 输入中文
        if (this.filterFlag && e.data && e.data.match(/[\u4e00-\u9fa5]+|[\u0370-\u03ff]+/)) {
          this.filterKey += e.data
        }
      }
    },
    getPosition(win) {
      win = win || window;
      var doc = win.document;
      var sel = doc.selection, range, rect;
      var x = 0, y = 0;
      if (sel) {
        if (sel.type != "Control") {
          range = sel.createRange();
          range.collapse(true);
          x = range.boundingLeft;
          y = range.boundingTop;
        }
      } else if (win.getSelection) {
        sel = win.getSelection();
        if (sel.rangeCount) {
          range = sel.getRangeAt(0).cloneRange();
          if (range.getBoundingClientRect) {
            range.collapse(true);
            rect = range.getBoundingClientRect();
            x = rect.left;
            y = rect.top;
          }
        }
      }
      return {left: x - demoPosition.left, top: y - demoPosition.top + 16}
    },
    // 开始监听输入，过滤数组
    createFilter() {
      this.filterFlag = true
      this.filterKey = ''
      this.preVal = null
      if (this.filterTimer) {
        clearTimeout(this.filterTimer)
      }
      const handle = () => {
        if (this.preVal !== this.filterKey) {
          this.preVal = this.filterKey
          if (this.filterKey) {
            this.searchList = this.totalList.filter(person => person.name.includes(this.filterKey))
          } else if (this.searchList.length != this.totalList.length) {
            // 查询值变成了空字符串，说明是查询全部
            this.searchList = this.totalList
          }
        }
        this.showSearchListBlock = true
        return this.filterTimer = setTimeout(handle, 500)
      }
      handle()
    },
    closeFilter() {
      this.filterKey = null
      this.preVal = ''
      this.filterFlag = false
      clearTimeout(this.filterTimer)
      this.showSearchListBlock = false
    },
    confirm(obj) {
      // this.inputVal = this.inputVal.substr(0, this.atIndex) + obj.name + ' '
      const selection = getSelection()
      if (this.lastEditRange) {
        selection.removeAllRanges()
        selection.addRange(this.lastEditRange)
      }
      const range = selection.getRangeAt(0)
      const textNode = range.startContainer;
      const rangeStartOffset = range.startOffset;
      // textNode.textContent = textNode.textContent.replace(this.filterKey, obj.name)
      
      textNode.replaceData(rangeStartOffset, this.filterKey.length, obj.name)
      range.setStart(textNode, rangeStartOffset + obj.name.length)
      range.collapse(true)
      selection.removeAllRanges()
      selection.addRange(range)
      this.lastEditRange = selection.getRangeAt(0)

      this.closeFilter()
    }
  }
}
</script>

<style scoped>
.text-input-pannel {
  position: relative;
  width: 400px;
  margin: 0 auto;
  border: 1px solid #0000ff57;
  border-radius: 5px;
}
.textarea {
  position: relative;
  width: 100%;
  /* height: 100%; */
  min-height: 200px;
  padding: 20px;
  border: none;
  word-break: break-all;
  overflow-x: hidden;
  outline: none;
  font-size: 14px;
  line-height: 14px;
  overflow-y: auto;
  resize: none;
  column-count: initial !important;
  white-space: pre-wrap;
  font-family: 'Courier New', Courier, monospace;
}
</style>