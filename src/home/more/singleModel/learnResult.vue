<template>
  <div class="learnAbility_info">
    <div class="score_header">
      <div class="return__icon" @click="returnBack">
        <i class="iconfont iconfanhui"></i>
      </div>
      <div class="title">学习力模型结果</div>
    </div>
    <div class="learnAbility_second">
      <p>结果统计：</p>
      <div ref="radar_chart" class="radar_chart"></div>
      <div class="type3_four_tip">
        <!--        <h4>说明：</h4>-->
        <li>驱动力：是来自内在的动力，也就是内心把一种事情做好的欲望。</li>
        <li>顺应力：将会帮助学习者更好地获得上述各种能力，同时，它将帮助学习者在遇到无序、未知和失败时能够保持不屈精神</li>
        <li>互惠力：主要体现在自己和他人之间的自主、观察与合作。他人分享或发表观点，是否有个人的思考在里面，学习他人优点，同时乐于分享。</li>
        <li>策应力：在学习过程中，能不能根据已有的问题或知识点，进行提问面对不同知识点推理并建立联系。</li>
        <li>调节力：也是反省力。学习时，会不会根据个人情况做一个相对合理的计划，在计划执行时或者执行后能不能按照实际情况做正确调整。</li>
      </div>
    </div>
  </div>
</template>
<script>
import {postLearnAnswer} from '@/api/index'
export default {
  data () {
    return {
      // learnQuesList: [],
      learnResult: [],
      rusultList: [],
      radarChart: ''
    }
  },
  computed: {
    answerList () {
      return JSON.parse(this.$route.query.list)
    }
  },
  mounted () {
    this.getLearnResult()
  },
  methods: {
    returnBack () {
      this.$router.go(-1)
    },
    getLearnResult () {
      postLearnAnswer({
        openid: '111',
        evaluateType: 3,
        result: this.answerList
      }).then(res => {
        console.log(res.data)
        this.learnResult = res.data.data
        this.getRadarChart()
        // this.
        // if (res.data.code === 0) {
        //
        // }
      })
    },
    getRadarChart () {
      this.rusultList.push(this.learnResult['accommodationForceNumScale'])
      this.rusultList.push(this.learnResult['drivingForceScale'])
      this.rusultList.push(this.learnResult['engineeringStressNumScale'])
      this.rusultList.push(this.learnResult['reciprocityNumScale'])
      this.rusultList.push(this.learnResult['resilienceNumScale'])
      console.log('3333', this.rusultList)
      this.radarChart = this.echarts.init(this.$refs.radar_chart)
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
          indicator: [
            // '驱动力', '顺应力', '策应力', '调节力', '互惠力'
            {name: '驱动力',
              max: 1,
              // 若将此属性放在radar下，则每条indicator都会显示圈上的数值，放在这儿，只在通信这条indicator上显示
              axisLabel: {
                show: true,
                fontSize: 12,
                color: '#838D9E',
                showMaxLabel: false, // 不显示最大值，即外圈不显示数字30
                showMinLabel: true // 显示最小数字，即中心点显示0
              }},
            {name: '顺应力', max: 1},
            {name: '策应力', max: 1},
            {name: '调节力', max: 1},
            {name: '互惠力', max: 1}
          ]
        },
        series: [{
          name: '能力比',
          type: 'radar',
          // areaStyle: {normal: {}},
          data: [
            {
              value: this.rusultList
              // name: '预算分配（Allocated Budget）'
            }
            // {
            //   value: [5000, 14000, 28000, 31000, 42000, 21000],
            //   name: '实际开销（Actual Spending）'
            // }
          ]
        }]
      })
    }
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
  /*.learnAbility_second {*/
  /*  margin-top: 10px;*/
  /*  padding: 5px 15px;*/
  /*}*/
  .learnAbility_second {
    margin: 25px 10px 15px;
    padding: 5px;
    box-shadow: 1px 1px 5px 1px rgba(66,185,130,0.4);
    border-radius: 10px;
    background-color: #fff;
    p {
      text-indent: 1.5em;
    }
  }
  .radar_chart {
    padding-top: 5px;
    height: 200px;
  }
  .type3_four_tip {
    /*border: 4px dashed rgba(66,185,130,0.4);*/
    border-radius: 10px;
    /*box-shadow: 1px 1px 5px 1px rgba(66,185,130,0.4);*/
    padding: 0 5px 10px;
    margin: 3px 15px 15px;
  }
  li {
    /*text-indent: 2em;*/
    color: #9c9c9c;
    font-size: 12px;
    line-height: 26px;
  }
</style>
