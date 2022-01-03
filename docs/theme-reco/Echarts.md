---
title: Echarts封装复制即用
date: 2021-12-21
---
```
<template>
  <div :class="className" :style="{ height: height, width: width }" />
</template>
<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from '@/components/mixins/resize'
import { string } from 'jszip/lib/support'

export default {
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    schedule: {
      type: Number,
      default: 0
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '100%'
    },
    color: {
      type: String,
      default: '#49d56b'
    },
    title: {
      type: String,
      default: '#49d56b'
    }
  },
  mounted() {
    this.initChart()
  },
  watch: {
    schedule() {
      this.initChart()
    },
    color() {
      this.initChart()
    }
  },
  methods: {
    initChart() {
      console.log(this.schedule)
      this.chart = echarts.init(this.$el, 'macarons')
      var option = {
        title: {
          show: true,
          text: this.schedule ? (this.schedule > 0? this.schedule + '%' + '\r\n\r\n' + this.title : '--' + '\r\n\r\n' + this.title) : '--' + '\r\n\r\n' + this.title,
          x: 'center',
          y: 'center',
          textStyle: {
            fontSize: '14',
            color: '#558DEA ',
            fontWeight: '500'
          }
        },
        series: {
          name: '',
          type: 'pie',
          radius: ['77%', '90%'],
          avoidLabelOverlap: true,
          hoverAnimation: false,
          label: {
            normal: {
              show: true,
              position: 'center',
              color: this.color,
              fontSize: 70
            },
            emphasis: {
              show: false
            }
          },
          labelLine: {
            normal: {
              show: false
            }
          },
          data: [
            {
              value: this.schedule,
              name: '',
              itemStyle: {
                color: this.schedule > 0 ? this.color : '#f2f2f7'
              }
            },
            {
              value: this.schedule < 0 ? 0 : this.schedule >= 100 ? 0 : 100 - this.schedule,
              name: '',
              itemStyle: {
                color: '#f2f2f7'
              }
            }
          ]
        }
      }
      this.chart.setOption(option)
    }
  }
}
</script>

