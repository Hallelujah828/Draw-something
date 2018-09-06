<template>
  <div class="container">
    <el-header class="header-wrap">
      <h1 class="title">
        <span class="red">你</span><span class="blue">画</span><span class="orange">我</span><span class="green">猜</span>
      </h1>
      <div class="count-down orange" v-if="isStart">
        <i class="el-icon-time"></i>
        倒计时29s
      </div>
    </el-header>
    <el-container>
      <el-aside class="aside" width="200px">
        <div class="aside-container">
          <div class="paint-title">玩家列表</div>
          <div class="preview-wrap-scroll">
            <div class="preview-wrap margin-avatar" v-for="(item, index) in players" :key="index">
              <div class="preview-avatar vel-flex" :style="{background:item.bgColor}">
                <p class="preview-name overflow-txt">{{item.name}}</p>
              </div>
            </div>
          </div>
        </div>
        <div class="aside-btm">
          <template v-if="isStart">
            <div class="btm-content" v-if="isPaint">游戏开始！您当前是绘画者，赶快开始绘画吧～</div>
            <div class="btm-content" v-if="!isPaint">当前绘画者为:<br>『 测试1』 <br> 您可以参与答题～ </div>
          </template>
          <template v-if="!isStart">
            <div class="btm-content">游戏未开始，点击下方按钮即可开始游戏～</div>
            <el-button type="primary" plain>我来画</el-button>
          </template>
        </div>
      </el-aside>
      <el-main class="flex-main">
        <div>
          <div class="paint-title">
            <template v-if="isStart">
              <i class="el-icon-bell"></i>
              {{isPaint?'关键词：猫':'提示：打一动物'}}
            </template>
          </div>
          <div class="paint-wrap">
            <canvas
              id="myCanvas"
              width="780px"
              height="400px"
              @mousedown="canvasDown($event)"
              @mouseup="canvasUp($event)"
              @mousemove="canvasMove($event)"
              @touchstart="canvasDown($event)"
              @touchend="canvasUp($event)"
              @touchmove="canvasMove($event)">
            </canvas>
            <div class="toolbar">
              <div class="toolbar-mask" v-if="isStart && !isPaint"></div>
              <div class="toolbar-header">工具栏</div>
              <div class="tool-item-wrap">
                <h5 class="toolbar-title">画笔颜色</h5>
                <ul class="item-wrap">
                  <li
                    v-for="(item, index) in colors"
                    :key="index"
                    class="color-item vel-flex"
                    :style="{ background: item }"
                    @click="setColor(item)"
                  >
                    <i class="el-icon-check" v-if="config.lineColor === item"></i>
                  </li>
                </ul>
              </div>
              <div class="tool-item-wrap">
                <h5 class="toolbar-title">画笔大小</h5>
                <div class="item-wrap">
                  <i
                    v-for="(item, index) in brushs"
                    :key="index"
                    class="el-icon-edit"
                    :class="[item.class, {'active': config.lineWidth == item.lineWidth}]"
                    @click="setBrush(item.lineWidth)"
                  ></i>
                </div>
              </div>
              <div class="answer" v-if="isStart && !isPaint">
                <el-input placeholder="请输入答案"></el-input>
                <el-button type="primary" class="answer-btn" plain>提交</el-button>
              </div>
            </div>
          </div>
        </div>
        <div class="msg-wrap">
          <div class="msg-item" v-for="(item,index) in messages" :key="index">
            玩家 <span class="msg-name">『 {{item.name}} 』</span> {{item.content}}
          </div>
        </div>
      </el-main>
    </el-container>
  </div>
</template>
<script>
export default {
  name: 'Main',
  data () {
    return {
      isStart: true, // 游戏开始
      isPaint: false, // 是绘画玩家
      players: [
        {
          name: '123',
          bgColor: '#409EFF'
        },
        {
          name: '测试',
          bgColor: 'rgba(19, 206, 102, 0.8)'
        },
        {
          name: '测试1',
          bgColor: 'rgba(255, 69, 0, 0.68)'
        }
      ],
      colors: ['#000', '#409EFF', '#ffc200', '#f32f15', 'rgba(255, 69, 0, 0.68)', '#5ab639'],
      brushs: [
        {class: 'small', lineWidth: 1},
        {class: 'middle', lineWidth: 6},
        {class: 'large', lineWidth: 12}
      ],
      messages: [
        {
          name: '123',
          content: '进入了房间！'
        },
        {
          name: '测试',
          content: '进入了房间！'
        },
        {
          name: '测试1',
          content: '进入了房间！'
        },
        {
          name: '测试1',
          content: '开始了游戏，考验默契的时候到了！'
        }
      ],
      // 初始工具栏配置
      config: {
        lineWidth: 1, // 画笔大小
        lineColor: '#000' // 画笔颜色
      },
      // 存储当前表面状态数组-上一步
      preDrawAry: [],
      // 存储当前表面状态数组-下一步
      nextDrawAry: [],
      // 中间数组
      middleAry: [],
      canvasMoveUse: true
    }
  },
  mounted () {
    const canvas = document.querySelector('#myCanvas')
    this.context = canvas.getContext('2d')
    this.initDraw()
    this.setCanvasStyle()
  },
  computed: {
  },
  methods: {
    // 设置颜色
    setColor (color) {
      this.config.lineColor = color
    },
    // 设置画笔大小
    setBrush (type) {
      this.config.lineWidth = type
    },
    initDraw () {
      const preData = this.context.getImageData(0, 0, 780, 400)
      // 空绘图页面进栈
      this.middleAry.push(preData)
    },
    // 设置绘画配置
    setCanvasStyle () {
      this.context.lineWidth = this.config.lineWidth
      this.context.strokeStyle = this.config.lineColor
    },
    // 当在屏幕中移动时即开始绘制准备
    beginPath (e) {
      const canvas = document.querySelector('#myCanvas')
      if (e.target !== canvas) {
        console.log('beginPath')
        this.context.beginPath()
      }
    },
    canvasMove (e) {
      if (this.canvasMoveUse) {
        console.log('canvasMove')
        const t = e.target
        let canvasX
        let canvasY
        canvasX = e.clientX - t.parentNode.offsetLeft
        canvasY = e.clientY - t.parentNode.offsetTop
        this.context.lineTo(canvasX, canvasY)
        this.context.stroke()
      }
    },
    // mouseup
    canvasUp (e) {
      console.log('canvasUp')
      const preData = this.context.getImageData(0, 0, 780, 400)
      if (!this.nextDrawAry.length) {
        // 当前绘图表面进栈
        this.middleAry.push(preData)
      } else {
        this.middleAry = []
        this.middleAry = this.middleAry.concat(this.preDrawAry)
        this.middleAry.push(preData)
        this.nextDrawAry = []
      }
      this.canvasMoveUse = false
    },
    // mousedown
    canvasDown (e) {
      console.log('canvasDown')
      this.canvasMoveUse = true
      // client是基于整个页面的坐标
      // offset是cavas距离顶部以及左边的距离
      const canvasX = e.clientX - e.target.parentNode.offsetLeft
      const canvasY = e.clientY - e.target.parentNode.offsetTop
      this.setCanvasStyle()
      // 清除子路径
      this.context.beginPath()
      this.context.moveTo(canvasX, canvasY)
      console.log('moveTo', canvasX, canvasY)
      // 当前绘图表面状态
      const preData = this.context.getImageData(0, 0, 780, 400)
      // 当前绘图表面进栈
      this.preDrawAry.push(preData)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .container{
    width: 1200px;
    height: 800px;
    margin: 0 auto;
    background: #fff;
    .preview-wrap-scroll{
      width: 100%;
      height: 410px;
      max-height: 410px;
      overflow: scroll;
      display: flex;
      flex-direction: column;
      align-items: center;
      box-shadow: 0 -10px 15px -10px rgba(0,0,0,0.1) inset;
    }
    .orange{
      color: rgba(255, 69, 0, 0.88);
    }
    .header-wrap{
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      position: relative;
      border-bottom: 1px solid #E5E5E5;
      box-sizing: border-box;
      .title{
        font-size: 20px;
        font-weight: bold;
        .red{
          color: crimson;
        }
        .blue{
          color: #409EFF;
        }
        .green{
          color: rgba(19, 206, 102, 0.8);
        }
      }
      .count-down{
        font-size: 18px;
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
      }
    }
    .aside{
      height: 740px;
      border-right: 1px solid #E5E5E5;
    }
    .el-icon-check{
      color: #fff;
      font-size: 12px;
    }
    .aside-container{
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px 0;
      box-sizing: border-box;
      background: #fff;
      .margin-avatar{
        margin-top: 10px;
      }
    }
    .aside-btm{
      padding: 20px;
      box-sizing: border-box;
      display: flex;
      justify-content: center;
      flex-direction: column;
      .btm-content{
        line-height: 1.8;
      }
    }
    .flex-main{
      display: flex;
      flex-direction: column;
    }
    .paint-title{
      height: 30px;
      line-height: 30px;
      font-size: 16px;
    }
    .paint-wrap{
      display: flex;
      margin-top: 10px;
      canvas{
        background: aliceblue;
      }
      .toolbar{
        position: relative;
        width: 200px;
        height: 400px;
        margin-left: 20px;
        display: flex;
        flex-direction: column;
        align-items: center;
        background:honeydew;
        padding: 10px;
        box-sizing: border-box;
        .toolbar-mask{
          width: 100%;
          height: 100%;
          top: 0;
          left: 0;
          position: absolute;
          z-index: 10;
          background: rgba(255,255,255,0.6);
        }
        .toolbar-header{
          font-weight: bold;
          font-size: 16px;
        }
        .tool-item-wrap{
          width: 100%;
          .item-wrap{
            display: flex;
            align-items: flex-end;
            justify-content: space-around;
            margin-top: 20px;
            .color-item{
              width: 15px;
              height: 15px;
              border-radius: 2px;
            }
          }
          .toolbar-title{
            font-size: 14px;
            margin-top: 20px;
          }
          .el-icon-edit.active{
            color: #ff8c00;
          }
        }
      }
      .small{
        font-size: 12px;
      }
      .middle{
        font-size: 16px;
      }
      .large{
        font-size: 20px;
      }
    }
    .msg-wrap{
      width: 100%;
      border: 1px solid #e5e5e5;
      margin-top: 20px;
      box-sizing: border-box;
      height: 240px;
      overflow: scroll;
      padding: 20px;
      .msg-item{
        margin-bottom: 10px;
        .msg-name{
          font-weight: bold;
        }
      }
    }
    .answer{
      position: absolute;
      bottom: 20px;
      padding: 10px;
      box-sizing: border-box;
      z-index: 20;
      .answer-btn{
        width: 100%;
        margin-top: 20px;
      }
    }
  }
</style>
