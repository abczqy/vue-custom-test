<template>
  <div class="task-tree-container">
    <header>
      <a-button style="float: left; margin: 10px 15px 0" @click="back" icon="double-left">返回</a-button>
      <div>任务关系树</div>
    </header>
    <div
      class="content"
      ref="wrapper"
      @mousewheel="mousewheel"
      @mousedown="mouseDown"
      @mouseleave="mouseUp"
      @mouseup="mouseUp"
      @mousemove="mouseMove">
      <!-- <my-chart ref="chart" style="height: 100%; width: 100%" :option="option"></my-chart> -->
      <canvas :width="width" :height="height" :style="{ width: `${width}px`, height: `${height}px`, left: `${left}px`, top: `${top}px`, transform: `translate(${initX + moveX}px, ${initY + moveY}px) scale(${zoom / 100})` }" ref="canvas"></canvas>
    </div>
  </div>
</template>

<script>
import { treeTask } from '@/api/apis/task/task'
import { createNamespacedHelpers } from 'vuex'
const { mapGetters } = createNamespacedHelpers('task')
export default {
  name: 'TaskTree',
  data() {
    return {
      base: [
        {
          name: '任务1',
          remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
          createTime: '2020年12月6日 18:00:00',
          importance: 'ZHONGJIN',
          progress: 70,
          chargerName: '张昆',
          id: 1,
          children: [
            {
              name: '子任务1',
              remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
              createTime: '2020年12月6日 18:00:00',
              importance: 'ZHONGJIN',
              progress: 78,
              chargerName: '陈凯',
              id: 2,
              children: [
                {
                  name: '任务1子任务',
                  remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                  createTime: '2020年12月6日 18:00:00',
                  importance: 'ZHONGJIN',
                  progress: 40,
                  chargerName: '陈韵如',
                  id: 3,
                  children: [
                    {
                      name: '任务1子任务1',
                      remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                      createTime: '2020年12月6日 18:00:00',
                      importance: 'ZHONGJIN',
                      progress: 80,
                      chargerName: '陈韵如',
                      id: '3-1'
                    },
                    {
                      name: '任务1子任务2',
                      remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                      createTime: '2020年12月6日 18:00:00',
                      importance: 'ZHONGJIN',
                      progress: 30,
                      chargerName: '陈韵如',
                      id: '3-2'
                    }
                  ]
                }
              ]
            },
            {
              name: '子任务2',
              remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
              createTime: '2020年12月6日 18:00:00',
              importance: 'ZHONGJIN',
              progress: 50,
              chargerName: '裴玉涛',
              id: 4,
              children: [
                {
                  name: '子子任务1',
                  remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                  createTime: '2020年12月6日 18:00:00',
                  importance: 'ZHONGJIN',
                  progress: 40,
                  id: 5,
                  chargerName: '陈韵如',
                  children: [
                    {
                      name: '四级任务2',
                      remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                      createTime: '2020年12月6日 18:00:00',
                      importance: 'ZHONGJIN',
                      progress: 40,
                      id: 10,
                      chargerName: '陈韵如'
                    },
                    {
                      name: '四级任务2',
                      remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                      createTime: '2020年12月6日 18:00:00',
                      importance: 'ZHONGJIN',
                      progress: 40,
                      id: 11,
                      chargerName: '陈韵如'
                    }
                  ]
                },
                {
                  name: '子子任务4',
                  remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                  createTime: '2020年12月6日 18:00:00',
                  importance: 'ZHONGJIN',
                  progress: 40,
                  id: 6,
                  chargerName: '陈韵如'
                },
                {
                  name: '子子任务5',
                  remark: '任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述任务描述',
                  createTime: '2020年12月6日 18:00:00',
                  importance: 'ZHONGJIN',
                  progress: 40,
                  id: 7,
                  chargerName: '陈韵如'
                }
              ]
            }
          ]
        }
      ],
      data: [],
      context: null,
      canvas: null,
      x: 0,
      y: 0,
      startX: 0,
      startY: 0,
      zoom: 100,
      moving: false,
      initX: 0,
      initY: 0,
      moveX: 0,
      moveY: 0,
      left: 0,
      top: 0,
      pointY: 0,
      pointX: 0,
      hasPlus: [],
      width: 0,
      height: 0
    }
  },
  methods: {
    back() {
      this.$router.back(-1)
    },
    fillRoundRect(width, height, radiu, left, top) {
      this.context.save()
      this.context.translate(left, top)
      this.context.beginPath()
      if (width === 0) {
        this.context.restore()
        return
      } else if (width < radiu) {
        this.context.lineTo(width, 0)
        this.context.arc(radiu, height - radiu, radiu, Math.PI / 2, Math.PI)
        this.context.lineTo(0, radiu)
        this.context.arc(radiu, radiu, radiu, Math.PI, Math.PI * 3 / 2)
        this.context.lineTo(0, width)
        this.context.arc(width - radiu, radiu, radiu, Math.PI * 3 / 2, Math.PI * 2)
        this.context.lineTo(width, height - radiu)
        this.context.closePath()
        this.context.restore()
        return
      }
      this.context.arc(width - radiu, height - radiu, radiu, 0, Math.PI / 2)
      this.context.lineTo(radiu, height)
      this.context.arc(radiu, height - radiu, radiu, Math.PI / 2, Math.PI)
      this.context.lineTo(0, radiu)
      this.context.arc(radiu, radiu, radiu, Math.PI, Math.PI * 3 / 2)
      this.context.lineTo(width - radiu, 0)
      this.context.arc(width - radiu, radiu, radiu, Math.PI * 3 / 2, Math.PI * 2)
      this.context.lineTo(width, height - radiu)
      this.context.closePath()
      this.context.restore()
    },
    // 画每个节点
    draw(data) {
      const x = data.x * 370 + this.startX
      const y = data.y * 225 + this.startY
      this.context.save()
      // 圆角矩形
      this.fillRoundRect(330, 152, 4, x, y)
      this.context.fillStyle = '#fff'
      this.context.fill()
      // 头像
      this.context.beginPath()
      this.context.arc(x + 32, y + 28, 16, 0, 2 * Math.PI)
      this.context.fillStyle = '#4878bd'
      this.context.fill()
      this.context.font = '12px 微软雅黑'
      this.context.fillStyle = '#fff'
      this.context.textAlign = 'center'
      this.context.textBaseline = 'middle'
      this.context.fillText(data.chargerName.substring(data.chargerName.length - 2), x + 32, y + 30)
      // title
      this.context.beginPath()
      this.context.fillStyle = '#333'
      this.context.font = '14px 微软雅黑 bold'
      this.context.textAlign = 'left'
      this.context.fillText(data.name, 58 + x, 30 + y)
      // 详情
      this.context.font = '12px 微软雅黑 normal'
      this.context.fillStyle = '#999'
      this.context.textAlign = 'left'
      this.context.textBaseline = 'top'
      const chr = data.remark.split('')
      const row = []
      let temp = ''
      for (let a = 0; a < chr.length; a++) {
        if (this.context.measureText(temp).width < 223) { } else {
          if (/[，。！》]/im.test(chr[a])) {
            temp += ` ${chr[a]}`
            a++
          }
          if (/[《]/im.test(chr[a - 1])) {
            temp = temp.substr(0, temp.length - 1)
            a--
          }
          row.push(temp)
          temp = ''
        }
        temp += chr[a] ? chr[a] : ''
      }
      row.push(temp)
      if (row.length > 2) {
        this.context.fillText(row[0], 58 + x, 48 + y)
        this.context.fillText(row[1].substring(0, row[1].length - 1) + '...', 58 + x, 68 + y)
      } else {
        this.context.fillText(row[0] || '', 58 + x, 48 + y)
        this.context.fillText(row[1] || '', 58 + x, 68 + y)
      }
      // 画标签
      this.fillRoundRect(64, 16, 2, x + 58, y + 88)
      this.context.strokeStyle = '#999'
      this.context.lineWidth = 1
      this.context.stroke()
      this.context.textAlign = 'center'
      this.context.textBaseline = 'middle'
      this.context.fillStyle = '#999'
      this.context.font = '10px 微软雅黑 normal'
      this.context.fillText(this.importance.find(o => o.value === data.importance).label, x + 90, y + 97)
      // 时间
      this.fillRoundRect(180, 16, 2, x + 132, y + 88)
      this.context.fillStyle = '#fafafa'
      this.context.fill()
      this.context.fillStyle = '#666'
      this.context.textAlign = 'center'
      this.context.textBaseline = 'middle'
      this.context.fillText('创建时间：' + data.createTime, x + 222, y + 97)
      // 横线
      this.context.beginPath()
      this.context.strokeStyle = '#f3f3f3'
      this.context.moveTo(x, 118 + y)
      this.context.lineTo(330 + x, 118 + y)
      this.context.stroke()
      // 进度条
      this.context.translate(16 + x, 130 + y)
      this.fillRoundRect(263, 9, 5)
      this.context.fillStyle = '#f2f2f2'
      this.context.fill()
      this.fillRoundRect(2.63 * data.progress, 9, 5)
      this.context.fillStyle = '#4878bd'
      this.context.fill()
      // 进度数字
      this.context.beginPath()
      this.context.fillStyle = '#666'
      this.context.font = '14px 微软雅黑 normal'
      this.context.textBaseline = 'middle'
      this.context.textAlign = 'center'
      this.context.fillText(data.progress + '%', 288, 5)
      this.context.restore()
    },
    // 遍历画节点
    drawItem() {
      this.data.forEach(item => {
        this.draw(item)
      })
    },
    renderData(data, index, weight, parent) {
      this.y = index + 1
      data.forEach((item, i) => {
        const x = weight - (data.length - 1) / 2 + i
        if (item.children) {
          this.renderData(item.children, index + 1, x, item.id)
        }
        // const otherI = this.data.findIndex(o => o.y === index && o.x === x)
        // console.log(otherI)
        // if (otherI > -1 && otherI !== i) {
        //   x += 1
        //   this.hasPlus.push(parent)
        // }
        this.data.push({
          ...item,
          y: index,
          x,
          parent
        })
        this.x = Math.max(Math.abs(x), this.x)
      })
      // if (hasPlus) {
      //   this.data.find(item => item.id === parent).x += 1
      // }
    },
    // 判断是否有重复节点
    secondRender() {
      let plus = []
      for (let i = this.y; i >= 0; i--) {
        const list = this.data.filter(item => item.y === i)
        list.forEach((item, j) => {
          if (list.findIndex(o => o.x === item.x) !== j) {
            item.x += 1
            plus.push(item.parent)
          }
        })
      }
      plus = this._.uniq(plus)
      console.log(plus, this.data)
      plus.forEach(p => {
        this.data.find(item => item.id === p).x += 1
      })
      this.x += 1
    },
    getData() {
      treeTask({ path: this.$route.query.path }).then(res => {
        if (res.code === 0) {
          this.renderData(this.base, 0, 0, this.base[0].id)
          this.secondRender()
          this.init()
        }
      })
    },
    init() {
      const width = (this.x * 2 + 1) * 370
      const height = (this.y + 1) * 152 + (this.y) * 73
      console.log(height)
      if (width < this.$refs.wrapper.offsetWidth - 200) {
        this.width = this.$refs.wrapper.offsetWidth
        this.startX = (this.canvas.width - 200) / 2 - 76
        this.left = 0
      } else {
        this.startX = (width - 200) / 2 - 155
        this.width = width + 200
        this.left = -((width + 200) - this.$refs.wrapper.offsetWidth) / 2 + 76
      }
      if (height < this.$refs.wrapper.offsetHeight - 100) {
        this.height = this.$refs.wrapper.offsetHeight
        this.startY = (this.$refs.wrapper.offsetHeight - height) / 2
        this.top = 0
      } else {
        this.startY = 50
        this.height = height + 100
        this.top = 0
      }
      this.zoom = Math.min(100, Math.max((width + 200) / this.$refs.wrapper.offsetWidth * 100, (height + 100) / this.$refs.wrapper.offsetHeight * 100))
      this.$nextTick(() => {
        this.drawLine()
        this.drawItem()
      })
    },
    // 画连线
    drawLine() {
      this.data.forEach(item => {
        if (item.children) {
          const pointX = this.startX + 370 * (item.x) + 170
          const pointY = (item.y + 1) * 152 + this.startY + (item.y) * 73
          this.context.save()
          this.context.beginPath()
          this.context.strokeStyle = '#999'
          this.context.lineWidth = 1
          item.children.forEach(child => {
            this.context.moveTo(pointX, pointY)
            const o = this.data.find(obj => obj.id === child.id)
            if (o.x === item.x) {
              this.context.lineTo(pointX, o.y * 225 + this.startY)
              return
            }
            // const control1X = pointX + o.x * 155
            const control1Y = pointY + 30
            const control2X = o.x * 370 + this.startX + 170
            const control2Y = pointY + 30
            this.context.bezierCurveTo(pointX, control1Y, control2X, control2Y, o.x * 370 + this.startX + 170, o.y * 225 + this.startY)
          })
          this.context.stroke()
          this.context.restore()
        }
      })
    },
    mousewheel(e) {
      e.stopPropagation()
      e.preventDefault()
      const wheelSize = -e.deltaY / 20
      this.wheel(wheelSize)
    },
    wheel(wheelSize) {
      const total = this.zoom + wheelSize
      if (total < 30) {
        this.zoom = 30
      } else if (total > 100) {
        this.zoom = 100
      } else {
        this.zoom = parseInt(total)
      }
      // this.init()
    },
    mouseDown(e) {
      e.stopPropagation()
      e.preventDefault()
      if (e.button === 0) {
        this.moving = true
        this.pointX = e.x
        this.pointY = e.y
        this.moveX = 0
        this.moveY = 0
      }
    },
    mouseUp(e) {
      e.stopPropagation()
      e.preventDefault()
      if (this.moving && e.button === 0) {
        this.moving = false
        this.moveX = 0
        this.moveY = 0
        this.initX = e.x - this.pointX + this.initX
        this.initY = (e.y - this.pointY) / this.zoom * 100 + this.initY
        this.pointX = null
        this.pointY = null
      }
    },
    mouseMove(e) {
      e.stopPropagation()
      e.preventDefault()
      if (this.moving) {
        this.moveX = e.x - this.pointX
        this.moveY = (e.y - this.pointY) / this.zoom * 100
      }
    }
  },
  mounted() {
    this.canvas = this.$refs.canvas
    this.context = this.canvas.getContext('2d')
    this.width = this.$refs.wrapper.offsetWidth
    this.height = this.$refs.wrapper.offsetHeight
    this.getData()
  },
  computed: {
    ...mapGetters(['importance'])
  },
  watch: {
    zoom() {
      this.width = this.$refs.wrapper.offsetWidth / (this.zoom / 100)
      this.height = this.$refs.wrapper.offsetHeight / (this.zoom / 100)
      this.$nextTick(() => {
        this.drawLine()
        this.drawItem()
      })
    }
  }
}
</script>
<style lang="less" scoped>
.task-tree-container {
  height: 100%;
  header {
    font-size: 24px;
    color: #333;
    text-align: center;
    line-height: 50px;
  }
  .content {
    height: calc(100% - 50px);
    overflow: hidden;
    position: relative;
    canvas {
      position: absolute;
      min-width: 100%;
      min-height: 100%;
    }
  }
}
</style>
