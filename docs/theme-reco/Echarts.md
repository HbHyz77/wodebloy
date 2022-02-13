---
title: 各种封装组件视图复制即用
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
```
---
keep-alive
---
```
<keep-alive>
    <router-view v-if="$route.meta.keepAlive">
        <!-- 这里是会被缓存的视图组件 -->
    </router-view>
</keep-alive>

<router-view v-if="!$route.meta.keepAlive">
    <!-- 这里是不被缓存的视图组件 -->
</router-view>
 -----------------------------------------------------
beforeRouteEnter(to, from, next) {
  // 路由导航钩子，此时还不能获取组件实例 `this`，所以无法在data中定义变量（利用vm除外）
  // 参考 https://router.vuejs.org/zh-cn/advanced/navigation-guards.html
  // 所以，利用路由元信息中的meta字段设置变量，方便在各个位置获取。这就是为什么在meta中定义isBack
  // 参考 https://router.vuejs.org/zh-cn/advanced/meta.html
  if(from.name=='page2'){
      to.meta.keepAlive=true;
      //判断是从哪个路由过来的，
      //如果是page2过来的，表明当前页面不需要刷新获取新数据，直接用之前缓存的数据即可
  }
  
  next();
},
--------------------------------------------------------
activated() {
  if(!this.$route.meta.isBack){
    // 如果isBack是false，表明需要获取新数据，否则就不再请求，直接使用缓存的数据
    this.getData();
  }
  // 恢复成默认的false，避免isBack一直是true，导致下次无法获取数据
  this.$route.meta.isBack=false
 
}
```
---
导航递归
---
```
<template>
  <div class="left-menu">
    <el-menu
     :default-active="$route.path"
    
      background-color="#1f3445"
      text-color="#ffffff"
      router
      v-if="items"
    >
     <template v-for="item in items">
            <!-- 含有子菜单 -->
            <template v-if="item.children && item.show ">
              <!-- 第一层 含有子菜单菜单 -->
              <el-submenu :index="item.path" :key="item.path">
                <template slot="title">
                  <!-- <i :class="item.meta.icon"></i> -->
                  <span slot="title">{{ item.meta.title }}</span>
                </template>
                <MenuList :items="item.children"></MenuList><!--递归调用-->
              </el-submenu>
            </template>
            <!-- 第一层 不含子菜单  -->
            <template v-else-if="item.show==false">
              <el-menu-item :index="item.path" :key="item.path">
                <!-- <i :class="item.meta.icon"></i> -->
                <span slot="title">{{ item.meta.title }}</span>
              </el-menu-item>
            </template>
          </template>
    </el-menu>
  </div>
</template>

<script>
export default {
    name:'MenuList',
    props: {
        items: Array,
    },

};
</script>

<style lang="less" scoped>
.left-menu {
  width: 200px;
  background-color: #1f3445;
  // overflow-y: scroll;
  // overflow-x: hidden;
  .el-menu {
    border-right: 0px;
    .iconfont {
      margin-right: 10px;
      color: #fff;
    }
    /deep/.el-menu {
      .el-menu-item {
        background-color: #1f3445 !important;
        &:hover {
          background-color: rgba(255, 255, 255, 0.1) !important;
        }
      }
    }
  }
}
</style>
```
---
el-element表格二次封装
---
