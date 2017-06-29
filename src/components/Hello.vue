<template>
  <div class="hello">
    <yd-navbar title="二本学校分数期望值"></yd-navbar>
    <p>
      <h3>标准分数线</h3>
      <br/>
      <yd-cell-group
        :title="standardScoreLine.year + '年'"
        :key="standardScoreLine"
        v-for="standardScoreLine in standardScoreLineList">
        <yd-cell-item>
          <span slot="left">一本线：</span>
          <yd-input slot="right" type="number" required max="20" v-model.number="standardScoreLine.firstScoreLine" placeholder="请输入分数"></yd-input>
        </yd-cell-item>
        <yd-cell-item>
          <span slot="left">二本线：</span>
          <yd-input slot="right" type="number" required max="20" v-model.number="standardScoreLine.secondScoreLine" placeholder="请输入分数"></yd-input>
        </yd-cell-item>
      </yd-cell-group>
    </p>
    <p>
      <template v-if="universityList.length > 0">
        <br/>
        <h3>学校投档线填写</h3>
        <br/>
      </template>
      <yd-cell-group
        :key="university"
        v-for="university in universityList">
        <yd-cell-item>
          <span slot="left">学校名称：</span>
          <yd-input slot="right" required max="20" v-model="university.name" placeholder="请输入学校名称"></yd-input>
        </yd-cell-item>
        <yd-cell-item v-for="scoreLine in university.scoreLines" :key="scoreLine">
          <span slot="left">{{ scoreLine.year }}年投档线：</span>
          <yd-input slot="right" required max="20" v-model="scoreLine.score" placeholder="请输入分数"></yd-input>
        </yd-cell-item>
        <yd-cell-item>
          <span slot="left">2017年期望投档线：</span>
          <div slot="right">
            <span>{{ university.expectScore }}</span>
            <yd-button type="primary" @click.native="countExpectScore(university)">计算</yd-button>
          </div>
        </yd-cell-item>
      </yd-cell-group>
      <yd-button-group>
        <yd-button type="primary" size="large" @click.native="addUniversity">新增学校</yd-button>
      </yd-button-group>
    </p>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        yearCountNum: 4,
        standardScoreLineList: [],
        universityList: []
      }
    },
    created () {
      this.standardScoreLineList = Array.from({length: this.yearCountNum}).map((val, index) => {
        return {
          year: 2016 - index,
          firstScoreLine: 0,
          secondScoreLine: 0
        }
      })
      this.standardScoreLineList = [{
        year: 2017,
        firstScoreLine: 505,
        secondScoreLine: 424
      }, {
        year: 2016,
        firstScoreLine: 517,
        secondScoreLine: 439
      }, {
        year: 2015,
        firstScoreLine: 526,
        secondScoreLine: 455
      }, {
        year: 2014,
        firstScoreLine: 522,
        secondScoreLine: 442
      }, {
        year: 2013,
        firstScoreLine: 495,
        secondScoreLine: 423
      }]
    },
    methods: {
      addUniversity () {
        this.universityList.push({
          name: '',
          expectScore: 0,
          scoreLines: Array.from({length: this.yearCountNum}).map((val, index) => {
            return {
              score: 0,
              year: 2016 - index
            }
          })
        })
      },
      countExpectScore (university) {
        const quotientCount = university.scoreLines.reduce((prev, curr) => {
          const year = curr.year
          const standardScoreLine = this.standardScoreLineList.find(item => {
            return year === item.year
          })
          if (!standardScoreLine) return prev
          const quotient = (curr.score - standardScoreLine.secondScoreLine) / (standardScoreLine.firstScoreLine - standardScoreLine.secondScoreLine)
          return prev + quotient
        }, 0)
        const currentYearStandardScoreLine = this.standardScoreLineList.find(item => {
          return 2017 === item.year
        })
        let expectScore = (currentYearStandardScoreLine.firstScoreLine - currentYearStandardScoreLine.secondScoreLine) * (quotientCount / university.scoreLines.length) + currentYearStandardScoreLine.secondScoreLine
        expectScore = (expectScore || 0).toFixed(2)
        university.expectScore = expectScore
        return expectScore
      }
    }
  }
</script>

