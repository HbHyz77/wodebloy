---
title: 封装组件视图复制即用1
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
三级路由菜单
---
```
<template>
  <el-menu :router="true" 
   background-color="#06184e" text-color="#fff"
    :unique-opened="true"  :default-active="$route.path"
   >
     <template v-for="(item,index) in DataDefault">
           <el-submenu :key="index" v-if="item.meta &&item.meta.AsideChild" :index="item.path" >
                  <template  #title>
                                <i :class="item.meta.icon"></i>
                              <span>{{item.meta.name}}</span>
                   </template>
                <template v-for="(itmeD,indexD) in item.children">
                 <el-submenu v-if="itmeD.meta &&itmeD.meta.AsideChild" :key="indexD" :index="`${item.path}/${itmeD.path}`">
                             <template #title>
                                <span>{{itmeD.meta.name}}</span>
                             </template>        
                             <el-menu-item v-for="(itmes,idnexs) in itmeD.children" :key="idnexs" :index="`${item.path}/${itmeD.path}/${itmes.path}`">
                                <template #title>
                                      <span>{{itmes.meta.name}}</span>
                              </template>
                         </el-menu-item>    
                   </el-submenu>
                       <el-menu-item v-else :key="indexD" :index="`${item.path}/${itmeD.path}`">
                                <template #title>
                             
                                      <span>{{itmeD.meta.name}}</span>
                              </template>
                      </el-menu-item>   
                </template>
            </el-submenu>
           <el-menu-item :key="index" v-else-if="item.meta" :index="item.path">
                      <template #title>
                           <i :class="item.meta.icon"></i>
                            <span>{{item.meta.name}}</span>
                      </template>
          </el-menu-item>    
     </template>
   </el-menu>

</template>

<script>
import DontaiRouter from "../router/DefaultRouter/DontaiRouter"
import staticRoutes from "../router/DefaultRouter/staticRoutes"
export default {
  data(){
    return{
      DataDefault:[...staticRoutes,...DontaiRouter]
    }
  }
};
</script>

<style scoped>
.el-menu {
  border-right: none;
  height: calc(100% - 60px);
}
</style>

```
---
el-elemnt 表格
---
```
<template>
  <div class="table_components">
    <div class="table_view_box">
      <div class="table_title_select" v-if="tableTitle">
        <el-popover
          placement="bottom-end"
          width="200"
          trigger="click"
          v-model="showSelect"
        >
          <el-button slot="reference" icon="el-icon-s-grid" circle></el-button>
          <div class="table_title_checked">
            <div class="title_all_box">
              <el-checkbox
                :indeterminate="isIndeterminate"
                v-model="checkAmount"
                @change="onChangeAmount"
                >全选</el-checkbox
              >
            </div>
            <div class="title_select_box">
              <div>
                <el-checkbox-group
                  v-model="titleChecked"
                  @change="onChangeItem"
                >
                  <template v-for="item in tableData.title">
                    <el-checkbox
                      :key="item.prop"
                      :label="item.name"
                    ></el-checkbox>
                  </template>
                </el-checkbox-group>
              </div>
            </div>
            <div class="title_btn_box" @click="tapSaveTitle">保存</div>
          </div>
        </el-popover>
      </div>
    
      <el-table
        class="table_box"
        id="out-table"
        ref="multipleTable"
        :height="tableData.height"
        :data="tableData.data"
        @sort-change="onSortChange"
        @selection-change="onSelectionChange"
        cell-class-name="table_td"     :fit="true"
        header-cell-class-name="table_title"   >
        <!-- 选择框 -->
    <el-table-column v-if="tableData.selection" type="selection" :selectable='selectInit' width="55" > </el-table-column>
        <!-- 内容 -->
        <template v-for="(item, index) in showTitles">
          <!-- 渲染内容 -->
          <!-- 多行列表渲染 -->
          <template v-if="item.list">
            <el-table-column
              :key="index"
              :label="item.name"
              :width="item.width"
              :min-width="item.minWidth"
              :show-overflow-tooltip="true"
              :fixed="item.fixed"
              
            >
              <template slot-scope="scope">
                <template v-for="(m, n) in scope.row[item.prop]">
                  <p class="over_hide" :key="`${index}-${n}`">
                    {{ item.key ? m[item.key] : m }}
                  </p>
                </template>
              </template>
            </el-table-column>
          </template>
          <template v-else-if="item.prop">
            <el-table-column
              :key="index"
              :label="item.name"
              :sortable="item.sort"
              :width="item.width"
              :min-width="item.minWidth"
              :show-overflow-tooltip="true"
              :fixed="item.fixed"
            >
              <template slot-scope="scope">
                <span v-if="item.prop == '$idx'">{{ scope.$index + 1 }}</span>
                <span
                  v-else-if="item.key"
                  :style="{
                    color: item.color ? item.color[scope.row[item.prop]] : '',
                  }"
                  >{{ item.key[scope.row[item.prop]] }}</span
                >
                <span
                  v-else
                  :style="{
                    color: item.color ? item.color[scope.row[item.prop]] : '',
                  }"
                >
                  {{ scope.row[item.prop] }}</span
                >
              </template>
            </el-table-column>
          </template>
          <template v-else-if="item.switch">
            <el-table-column
              :key="index"
              :label="item.name"
              :width="item.width"
              :fixed="item.fixed"
            >
              <template slot-scope="scope">
                <el-switch
                  v-model="scope.row[item.switch]"
                  active-value="1"
                  inactive-value="0"
                  @change="onSwitchChange(scope.row, item.switch)"
                ></el-switch>
              </template>
            </el-table-column>
          </template>
          <template v-else-if="item.btns">
            <el-table-column
              :key="index"
              :label="item.name"
              :width="item.width"
              :fixed="item.fixed"
            >
              <template slot-scope="scope">
                <template v-for="(m, n) in item.btns">  
                  <template >
                    <template >
                      <el-button
                        :key="`${index}-${n}`"
                        type="text"
                        size="small"
                        :disabled="(m.is_show && scope.row[m.is_show] != '1')"
                        @click="tapEditBtn(scope.row, m.func, scope.$index)"
                        :style="{ color: m.color }"
                      >
                        {{ m.prop ? m.nameKeys[scope.row[m.prop]] : m.name }}
                      </el-button>
                    </template>
                  </template>
                </template>
              </template>
            </el-table-column>
          </template>
        </template>
      </el-table>
    </div>
    <el-pagination
      class="table_pagination"
      @size-change="onhandleSizeChange"
      @current-change="onPageChange"
      :current-page.sync="tableData.page"
      :page-sizes="[10, 20, 30, 40]"
      :page-size="100"
      layout="total, sizes, prev, pager, next, jumper"
      :total="tableData.total"
    >
    </el-pagination>
   
    <!-- 查看   -->
    <el-dialog class="preview_image_box" :visible.sync="dialogView">
      <img class="preview_image" :src="preView" alt="" />
    </el-dialog>
  </div>
</template>
<script>

export default {
  props: {
    rid: Array,
    tableTitle: String, //是否自定义表头
    tableData: {
      height: Number | String, //height
      size: Number, //*显示条数
      page: Number, //*当前页数
      total: Number, //*数据总共条数
      selection: Boolean, //是否选择
      title: [
        {
          //*
          name: String, //显示名字
          prop: String, //对应data的字段名
          sort: Boolean | String, //是否排序
          switch: String, //渲染switch
          image: String, //image: 图片
          list: Boolean, //列表多行渲染
          fixed: String, //固定列
          btns: [
            //操作
            {
              name: String,
              func: String,
              color: String,
            },
          ],
        },
      ],
      data: Array,
    },
  },
  data() {
    return {
      preView: "",
      showSelect: false,
      dialogView: false,
      titleChecked: [],
      checkAmount: false,
      isIndeterminate: false,
      showTitles: [],
      tableAll: {},
      menu: this.menudata,
      hylist: [],
    };
  },
  watch: {
    tableData: {
      handler(val) {
        if (val.data && val.data.length) {
          let checked = val.data.filter((item) => item.check);
          this.toggleSelection(checked);
        }
      },
      deep: true,
    },
  },
 
  mounted() {
  
    let checked = this.tableData.data.filter((item) => item.check);
    this.toggleSelection(checked);
  },
  methods: {
    toggleSelection(rows) {
      if (rows) {
        rows.forEach((row) => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
      if (this.tableTitle) {
        this.renderTitle();
      } else {
        this.showTitles = this.tableData.title;
      
      }
    },
    handleCheckedCitiesChange(value) {
      console.log(value);
    },
    onhandleSizeChange(data) {
   
      this.$emit("SizeChange", data);
    },
    selectInit(row,index){
      
        if(row.is_use && row.is_use =='2'){
            return false 
        }else{    
            return true  //可勾选
        }
},
    onSortChange(column) {
      this.$emit("emitSort", column);
    },
    onPageChange(val) {
      this.$emit("emitPage", val);
    },
    onSelectionChange(selection) {
      this.$emit("emitSelection", selection);
    },
    tapEditBtn(row, func, index) {
      console.log(12345);
      console.log(row, func);
      this.$emit("emitEditBtn", { row, func, index });
    },
    onSwitchChange(row, prop) {
      this.$emit("emitSwitch", { row, prop });
    },
    tapImage(src) {
      this.preView = src;
      this.dialogView = true;
    },
    format(percentage) {
      
      return percentage === 100 ? "100%" : `${percentage}%`;
    },
    // titles
    renderTitle() {
      let tables = window.localStorage.getItem("TABLE_TITLE");
      if (!tables) tables = "{}";
      this.tableAll = JSON.parse(tables);
      if (!this.tableAll[this.tableTitle]) {
        this.showTitles = this.tableData.title;
      } else {
        this.showTitles = this.tableAll[this.tableTitle];
      }
      this.titleChecked = this.showTitles.map((item) => item.name);
      if (this.showTitles.length == this.tableData.title.length) {
        this.checkAmount = true;
        this.isIndeterminate = false;
      } else if (this.showTitles.length) {
        this.isIndeterminate = true;
        this.checkAmount = false;
      }
    },
    onChangeAmount(val) {
      this.titleChecked = val
        ? this.tableData.title.map((item) => item.name)
        : [];
      this.isIndeterminate = false;
    },
    onChangeItem(value) {
      let checkedCount = value.length;
      this.checkAmount = checkedCount === this.tableData.title.length;
      this.isIndeterminate =
        checkedCount > 0 && checkedCount < this.tableData.title.length;
    },
    tapSaveTitle() {
      this.showTitles = this.tableData.title.filter((item) =>
        this.titleChecked.includes(item.name)
      );
      this.tableAll[this.tableTitle] = this.showTitles;
      window.localStorage.setItem("TABLE_TITLE", JSON.stringify(this.tableAll));
      this.showSelect = false;
    },
  },
};
</script>
<style lang="less" >
/deep/.el-tooltip__popper {
  max-width: 80%;
}

.table_components {
  width: 100%;
  height: 100%;
  .table_view_box {
    width: 100%;
    height: 100%;
    height: calc(100% - 60px);
    overflow-x: auto;
    overflow-y: auto;
    .el-table .cell {
      overflow: hidden;
      color: #333;
      display: flex;
    flex-wrap: nowrap;
    }
    .table_title_select {
      width: 100%;
      height: 40px;
      display: flex;
      padding: 0 10px;
      background: #d6d8dc;
      justify-content: flex-end;
      border-bottom: 1px solid #fff;
    }
  }
  .titles {
    width: 35px;
    height: 35px;
    position: relative;
    top: 3px;
  }
  .table_box {
    width: 100%;
    border-collapse: collapse;
  }

  .table_title {
    color: #333;
    font-size: 14px;
    padding: 8px 0 !important;
    // border-left: 1px solid #fff;
    background-color: #c0c4cc !important;
    .ascending {
      border-bottom-color: #606266;
    }
    .descending {
      border-top-color: #606266;
    }
    &:first-child {
      border: none;
    }
    .el-checkbox {
      background: initial !important;
    }
  }
  .table_td {
    height: 40px;
    font-size: 12px;
    padding: 0 !important;
    // border-left: 1px solid #ebeef5;
  }
  .table_pagination {
    margin-top: 26px;
    text-align: center;
  }
  .table_bor_left {
    border-left: 1px solid #d7d9dc;
  }
  .over_hide {
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  .table_image {
    max-width: 25px;
    max-height: 25px;
    border-radius: 5px;
  }
}
.table_image-ss {
  display: flex;
  align-self: center;
  // justify-content: space-around;
  .table-title {
    width: 90%;
    display: flex;
    align-self: center;
    justify-content: flex-start;
  }
  .table-imgs {
    width: 40px;
    display: flex;
    align-self: center;
    // justify-content: center;
  }
}
.table_title_checked {
  width: 100%;
  .title_all_box {
    width: 100%;
    height: 30px;
    border-bottom: 1px solid #ebeef5;
  }
  .title_select_box {
    width: 100%;
    height: 200px;
    overflow-y: auto;
  }
  .title_btn_box {
    width: 100%;
    height: 30px;
    cursor: pointer;
    line-height: 30px;
    text-align: center;
    border-top: 1px solid #ebeef5;
    &:hover {
      color: #409eff;
    }
  }
  .el-checkbox {
    width: 100%;
    margin-right: 0;
    line-height: 30px;
  }
}

.preview_image_box {
  display: flex;
  -ms-display: flex;
  align-items: center;
  -ms-align-items: center;
  .el-dialog {
    width: auto;
    margin-top: 0 !important;
  }
  .preview_image {
    display: block;
    margin: 0 auto;
    max-width: 800px;
  }
}
.el-dropdown-link {
  cursor: pointer;
  color: #409eff;
  font-size: 12px;
  margin-left: 10px;
}
.el-icon-arrow-down {
  font-size: 12px;
}
.butonssssw {
  width: 80px;
  text-align: center;
}
.wwwwwww {
  margin-left: 10px;
  // height: 30px;
}
</style>
```
关系图
---

```
<template>
  <div style="width: 100%; height:100%">
    <div id="main" style="width: 100%; height:100%"></div>
  </div>
</template>
  methods: {
    echartss() {
      this.$nextTick(() => {
        var chartDom = document.getElementById("main");
        var myChart = echarts.init(chartDom);
        var option;
        option = {
           title: {
			        text: '应用关系：'+this.title,
			        subtext: '数据来自人立方',
			        top: 'bottom',
			        right: '15px'
			    },
          tooltip: {},
          legend: {
            top:"15px",
            data: this.legend_data,
          },
          animationDuration: 1500,
          animationEasingUpdate: "quinticInOut",
          series: [
            {
              name: "应用关系",
              type: "graph",
              layout: "force",
              // force: {
              //   //力引导图基本配置
              //   repulsion: 600, //节点之间的斥力因子。支持数组表达斥力范围，值越大斥力越大。
              //   gravity: 0.03, //节点受到的向中心的引力因子。该值越大节点越往中心点靠拢。
              //   edgeLength: [100, 300], //边的两个节点之间的距离，这个距离也会受 repulsion。[10, 50] 。值越小则长度越长
              //   // layoutAnimation : true
              // },
              data: this.nodes,
              categories: this.category,
              links: this.links,
              roam: true,
              focusNodeAdjacency: true,
              itemStyle: {
                borderColor: "#fff",
                borderWidth: 1,
                shadowBlur: 10,
                shadowColor: "rgba(0, 0, 0, 0.3)",
              },
              label: {
                show: true,
                textStyle: {
                  color: "#333",
                },
                position: "right",
                formatter: "{b}",
              },
              nodeStyle : {
							brushType : 'both',
							borderColor : 'rgba(255,215,0,0.4)',
							borderWidth : 1
					  	}, 
              lineStyle: {
               normal: {
						        curveness: 0.3
						    }
              },
             force: {
			                repulsion: 2000,
			            }
            },
          ],
        };
        option && myChart.setOption(option);
      });
    },
  },
};
</script>

<style lang="less" scoped>
</style>


