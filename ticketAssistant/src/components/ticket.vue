<template lang="html">
  <div class="box">
    <div class="notification is-success" v-show="!show">
        <h1>{{name}}</h1>
        <p>{{date}}点</p>
    </div>
    <div class="iframe" v-show="show">
      <iframe :src="src"></iframe>
    </div>
    <div class="draw_button">
      <a class="button is-warning" @click="draw">开始抢券</a>
      <a class="button is-primary" @click="stop">结束抢券</a>
    </div>
    <div class="control">
      <div class="select is-primary selected">
          <select v-model="faultDate">
            <option :value="time.value" v-for="time in times">{{ time.value }}</option>
          </select>
      </div>
        <a class="button is-warning" @click="timeDraw">定时开始</a>
    </div>
    <div class="control" v-show='chzhiIsShow'>
      距离开始还有{{ chazhiShow }}
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      show: false,
      flag: true,
      src: '',
      faultDate: '',
      chazhi: '',
      chazhiShow: '',
      chzhiIsShow: false,
      times: [
        {
          value: '0'
        },
        {
          value: '8'
        },
        {
          value: '10'
        },
        {
          value: '12'
        },
        {
          value: '14'
        },
        {
          value: '16'
        },
        {
          value: '18'
        },
        {
          value: '20'
        },
        {
          value: '22'
        }
      ]
    }
  },
  props: {
    name: {
      type: String
    },
    api: {
      type: String
    },
    date: {
      type: String
    },
    time: {
      type: Number
    }
  },
  created () {
    this.faultDate = this.date.split(',')[0]
  },
  methods: {
    timer () {
      let a = setInterval(() => {
        if (this.src) {
          this.src = ''
        } else {
          this.src = this.api
        }
        if (this.flag === false) {
          clearInterval(a)
        }
      }, this.time / 2)
      setTimeout(() => {
        clearInterval(a)
        this.show = !this.show
      }, 20000)
    },
    draw () {
      this.show = !this.show
      this.timer()
    },
    stop () {
      this.flag = false
      this.show = !this.show
    },
    timeDraw () {
      this.chzhiIsShow = true
      const nowTime = new Date()
      const timestamp = nowTime.getTime()
      let dingshiTime = Date.parse(nowTime.toLocaleDateString().replace(/\//g, '-') + ' ' + this.faultDate + ':00:00')
      this.chazhi = Math.ceil((dingshiTime - timestamp) / 1000)
      console.log(this.chazhi)
      setInterval(() => {
        this.chazhi -= 1
      }, 1000)
      this.chazhiShow = this.chazhi < 0 ? '您选择的时间已经过去' : this.chazhi < 60 ? this.chazhi + '秒' : this.chazhi < 3600 ? parseInt(this.chazhi / 60) + '分钟' + Math.parseFloat(this.chazhi / 60) + '秒' : this.chazhi
      if (dingshiTime - timestamp === 0) {
        this.draw()
      }
    }
  }
}
</script>

<style lang="scss">
.box {
    width: 270px;
    margin-left: 10px;
    .notification {
        line-height: 2;
        min-height: 100px;
        text-align: center;
        h1 {
            font-size: 20px;
        }
    }
    .iframe {}
    .draw_button {
        display: flex;
        justify-content: space-around;
    }
    .control {
      display: flex;
      justify-content: space-around;
      padding-top: 10px;
      text-align: center;
    }
}
</style>
