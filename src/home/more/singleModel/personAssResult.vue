<template>
  <div class="learnAbility_info">
    <div class="score_header">
      <div class="return__icon" @click="returnBack">
        <i class="iconfont iconfanhui"></i>
      </div>
      <div class="title">人格测试结果</div>
    </div>
    <div class="resultInfo" ref="resultInfo">
      <div style="padding-bottom: 20px;">
        <div ref="radar_chart" class="radar_chart"></div>
        <div v-for="(item, index) in personAssResult" :key="index" class="result_item">
          {{item.bigFive.describe}}
          <div>{{item.bigFive.level}}</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import {postPersonResult} from '@/api/index'
import BScroll from 'better-scroll'
// import bus from '@/api/vueBus.js'
export default {
  data () {
    return {
      personAssResult: [],
      dataResult: [],
      dataEchart: [],
      radarChart: '',
      resultScroll: null
    }
  },
  computed: {
    answerList () {
      return JSON.parse(this.$route.query.list)
    }
  },
  mounted () {
    this.postPersonResult()
  },
  methods: {
    returnBack () {
      this.$router.go(-1)
    },
    init () {
      this.$nextTick(() => {
        // console.log()
        this.resultScroll = new BScroll(this.$refs.resultInfo, {
          click: true,
          bounce: false
        })
        console.log(this.resultScroll)
      })
    },
    postPersonResult () { // 获取结果数据
      postPersonResult({
        openid: '111',
        result: this.answerList
        // result: JSON.stringify(this.choice)
      }).then(res => {
        if (res.data.code === 0) {
          this.personAssResult = res.data.data
          for (let i in this.personAssResult) {
            const each = {text: this.personAssResult[i].bigFive.title, max: this.personAssResult[i].fullScore}
            this.dataResult.push(each)
            this.dataEchart.push(this.personAssResult[i].score)
          }
          this.drawPersonAssResult()
          // this.init()
        }
        console.log('结果：', res.data)
      })
    },
    drawPersonAssResult () {
      console.log('3333', this.personAssResult)
      this.radarChart = this.echarts.init(this.$refs.radar_chart)
      console.log('3333', this.radarChart)
      this.radarChart.setOption({
        // title: {
        //   text: '基础雷达图'
        // },
        // 图表的位置
        grid: {
          position: 'center'
        },
        tooltip: {
          // 雷达图的tooltip不会超出div，也可以设置position属性，position定位的tooltip 不会随着鼠标移动而位置变化，不友好
          confine: true,
          enterable: true // 鼠标是否可以移动到tooltip区域内
        },
        legend: {
          data: ['预算分配（Allocated Budget）']
        },
        radar: { // polar
          nameGap: 5, // 图中工艺等字距离图的距离
          center: ['50%', '50%'], // 图的位置
          // shape: 'circle',
          name: {
            textStyle: {
              color: '#fff',
              backgroundColor: '#999',
              borderRadius: 3,
              padding: [3, 5]
            }
          },
          indicator: this.dataResult
        },
        series: [{
          name: '能力比',
          type: 'radar',
          // areaStyle: {normal: {}},
          data: [
            {
              value: this.dataEchart,
              label: {
                show: true,
                formatter: function (params) {
                  return params.value
                }
              }
            }
          ]
        }]
      })
      console.log('3333', this.radarChart)
      // this.init()
      this.$nextTick(() => {
        this.init()
        // this.resultScroll && this.resultScroll.refresh()
      })
    },
  }
}
</script>
<style scoped lang="scss">
  .learnAbility_info {
    height: 100%;
    /*width: 100%;*/
    display: flex;
    flex-direction: column;
    background: #fff;
  }
  .score_header {
    position: relative;
    font-size: 16px;
    width: 100%;
    background: linear-gradient(to right, #00d2ff 0%, #37a3ff 100%);
    /*background-color: #19bdff;*/
    color: #fff;
    height: 45px;
    line-height: 45px;
    flex: none;
    z-index: 1;
  }
  .return__icon{
    position: absolute;
    margin-left: 15px;
  }
  .iconfanhui {
    font-size: 18px;
  }
  .title {
    text-align: center;
    font-weight: bold;
  }
  .resultInfo {
    margin-top: 15px;
    height: calc(100% - 50px);
    overflow: hidden;
    /*position: absolute;*/
    /*top: 45px;*/
    /*left: 0;*/
  }
  .radar_chart {
    padding-top: 5px;
    height: 200px;
    position: relative;
  }
  .result_item {
    margin: 5px 15px;
    padding: 2px;
    font-size: 14px;
    line-height: 26px;
    text-indent: 2em;
    color: #3c3c3c;
  }
</style>
