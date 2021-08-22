<template>
  <div v-show="isShow" class="info-list-pannel" ref="pannel" @scroll="onScroll" :style="{'max-height': defaultSetting.height + 'px', 'width': defaultSetting.width + 'px', 'left': position.left + 'px', 'top': position.top + 'px'}">
    <div class="list-bg" :style="{'height': infoList.length * defaultSetting.lineHeight + 'px'}">
    </div>
    <div class="info-list" ref="infoListContent">
      <div class="info-content" v-for="(item, index) in showList" :key="index" :style="{'height': defaultSetting.lineHeight + 'px'}" @click="confirm(item)">
        <img v-if="item.img" class="avator" :src="item.img">
        <div class="name">{{item.name}}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    infoList: {
      type: Array
    },
    position: {
      type: Object
    },
    isShow: {
      type: Boolean
    }
  },
  watch: {
    isShow() {
      if (this.isShow) {
        this.$nextTick(() => {
          this.$refs.pannel.scrollTop = 0
          this.$refs.infoListContent.style.transform = `translateY(0)`
          //避免重复渲染首屏
          this.cacheLineHeight = [-1, 1]
        })
        this.showList = this.infoList.slice(0, this.partSize * 2)
      }
    },
    infoList() {
      this.$nextTick(() => {
        this.$refs.pannel.scrollTop = 0
        this.$refs.infoListContent.style.transform = `translateY(0)`
        //避免重复渲染首屏
        this.cacheLineHeight = [-1, 1]
      })
      this.showList = this.infoList.slice(0, this.partSize * 2)
    }
  },
  mounted() {
    this.defaultSetting.height = this.defaultSetting.lineHeight * this.partSize
  },
  data() {
    return {
      defaultSetting: {
        width: 150,
        height: 0,
        lineHeight: 40
      },
      partSize: 5, // 一屏展示多少条数据
      showList: [],
      ticking: false,
      cacheLineHeight: [],
      frameId: 0
    }
  },
  methods: {
    confirm(obj) {
      cancelAnimationFrame(this.frameId)
      this.$emit('confirm', obj)
    },

    onScroll(e) {
      console.log(e.target.scrollTop)
      if (this.ticking) {
        return
      }
      this.ticking = true
      this.frameId = requestAnimationFrame(() => {
        this.ticking = false
      })
      this.updateList(e.target.scrollTop)
    },
    updateList(distance) {
      if (this.cacheLineHeight.length > 0) {
        if (distance > this.cacheLineHeight[0] 
          && distance < this.cacheLineHeight[1]) {
          // 一定范围内不用更新数组
          return
        }
      }

      const startIndex = this.getStartIndex(distance)
      // 上一屏
      const upperIndex = startIndex - this.partSize > 0 ? startIndex - this.partSize : 0
      const upperList = this.infoList.slice(upperIndex, startIndex)
      this.$refs.infoListContent.style.transform = `translateY(${(upperIndex) * this.defaultSetting.lineHeight}px)`
      // 视口屏
      const curList = this.infoList.slice(startIndex, startIndex + this.partSize)
      // 下一屏
      const nextIndex = startIndex + this.partSize > this.infoList.length - 1 ? this.infoList.length : startIndex + this.partSize
      const nextList = this.infoList.slice(nextIndex, nextIndex + this.partSize)

      this.showList = [...upperList, ...curList, ...nextList]
      this.cacheLineHeight = [this.defaultSetting.lineHeight * Math.floor((upperIndex + this.partSize / 2)), this.defaultSetting.lineHeight * Math.floor((startIndex + this.partSize / 2))]
    },
    getStartIndex(scrollTop) {
      let start = 0, end = this.infoList.length - 1, lineHeight = this.defaultSetting.lineHeight
      while(start < end) {
        const mid = Math.floor((start + end) / 2)
        let top = mid * lineHeight
        if (top <= scrollTop && (top + lineHeight) > scrollTop) {
          start = mid
          break
        } else if (scrollTop >= top + lineHeight) {
          start = mid + 1
        } else if (scrollTop < top) {
          end = mid - 1
        }
      }
      return start
    }
  }
}
</script>

<style scoped>
.info-list-pannel {
  position: absolute;
  background-color: #fff;
  box-shadow: 1px 1px 10px #99999952;
  overflow-y: scroll;
  border-radius: 4px;
}
.list-bg {
  width: 100%;
}
.info-list {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
.info-content {
  width: 100%;
  padding: 2px 0 2px 4px;
  display: flex;
  align-items: center;
  justify-content: flex-start;
}
.avator {
  margin-right: 10px;
}
</style>