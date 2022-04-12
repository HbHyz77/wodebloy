---
title: 封装 饼图 折线图 等等
date: 2020-05-29
---
 饼图
---
```
   <template>
  <div :class="className" style="width: 100%; height: 100%"></div>
</template>

<script>
import * as echarts from "echarts";
export default {
  props: {
    className: {
      type: String,
      default: "mayin",
    },
    datapie: {
      type: Array,
      default: () => {
        return [];
      },
    },
    color:{
      type:Array,
       default: () => {
        return [];
      },
    },
    text: {
      type: String,
     
    },
  },
  data() {
    return {};
  },
  watch: {
    datapie() {
      this.getdata();
    },
  },
  mounted() {
    this.getdata();
  },
  methods: {

    getdata() {
     
        let dataY=[];
        let sss=0;
         this.datapie.forEach((item)=>{
            sss+=parseInt(item.value)
        })
        dataY=this.datapie
       
        var options = {
        color: this.color,
        title: {
          text:this.text,
          subtext:sss,
          textStyle: {
            color: " rgba(174, 174, 178, 100)",
            fontSize: 16,
          },
              subtextStyle: {
             fontSize: 25,
             
            color: "black",
          },
          x: "21%",
          textAlign: "center",
          y: "35%",
        },
       
        legend: {
          orient: "vertical",
          icon: "circle", //图例形状
          padding: 0,
          bottom: "center",
          right: 40,
          itemWidth: 12, //小圆点宽度
          itemHeight: 12, // 小圆点高度
          itemGap: 21, // 图例每项之间的间隔。[ default: 10 ]横向布局时为水平间隔，纵向布局时为纵向间隔。
          textStyle: {
            fontSize: 14,
            color: "black",
          },
        },
        series: [
          {
            type: "pie",
            center: ["22%", "50%"], //圆心的位置
            top: "0%", //单单指的饼图距离上面的距离，top越大饼图越小
            left: "0%", //单单指的饼图距离左面的距离，会改变饼图的大小
            radius: ["60%", "75%"], //环形图的本质就在这里 [内半径!=0，外半径] 外半径越大，圆越大
            avoidLabelOverlap: false, //做同心圆用到
            clockwise: false, //顺时针排列
            startAngle: 90, //起始角度 影响不大

            label: {
              show: false, //false不显示饼图上的标签
              position: "center", //inside（在饼图上显示）,outside(默认就会出现引导线) center
            },

            data: dataY,
          },
        ],
      };
      this.$nextTick(() => {
        let myChartlist = echarts.init(
          document.querySelector("." + this.className)
        );
        myChartlist.setOption(options);
        window.addEventListener("resize", function () {
          myChartlist.resize();
        });
      });
    },
  },
};
</script>

<style scoped>
</style>
```
---
折线图
---
```
<template>
  <div @click="wwww" id="map" class="hello"></div>
</template>
<script>
import * as echarts from "echarts";
import world from "./json/world.json";
import china from "./json/china.json";
import { apiUrl, requestAxiosPost } from "@/utils/common";
var myChartss
export default {
  props: {
    echartsmap:{
      type: Array,
      default: function(){
         return []
       }
    },
  },
  data() {
    return {
      value: "world",
      //   注册地图json
      jsonMap: {
        "world": world,
        "中国": china,
      },
   
    
      // 全球地域中文映射
      nameMap: {
        "Afghanistan": "阿富汗",
        "Albania": "阿尔巴尼亚",
        "Algeria": "阿尔及利亚",
        "Andorra": "安道尔",
        "Angola": "安哥拉",
        "Antarctica": "南极洲",
        "Antigua and Barbuda": "安提瓜和巴布达",
        "Argentina": "阿根廷",
        "Armenia": "亚美尼亚",
        "Australia": "澳大利亚",
        "Austria": "奥地利",
        "Azerbaijan": "阿塞拜疆",
        "The Bahamas": "巴哈马",
        "Bahrain": "巴林",
        "Bangladesh": "孟加拉国",
        "Barbados": "巴巴多斯",
        "Belarus": "白俄罗斯",
        "Belgium": "比利时",
        "Belize": "伯利兹",
        "Benin": "贝宁",
        "Bermuda": "百慕大",
        "Bhutan": "不丹",
        "Bolivia": "玻利维亚",
        "Bosnia and Herzegovina": "波斯尼亚和黑塞哥维那",
        "Botswana": "博茨瓦纳",
        "Brazil": "巴西",
        "Brunei": "文莱",
        "Bulgaria": "保加利亚",
        "Burkina Faso": "布基纳法索",
        "Burundi": "布隆迪",
        "Cambodia": "柬埔寨",
        "Cameroon": "喀麦隆",
        "Canada": "加拿大",
        "Cape Verde": "佛得角",
        "Central African Rep.": "中非共和国",
        "Côte d'Ivoire": "象牙海岸",
        "Chad": "乍得",
        "Chile": "智利",
        "China": "中国",
        "Colombia": "哥伦比亚",
        "Comoros": "科摩罗",
        "Dem. Rep. Congo": "刚果共和国",
        "Congo": "刚果",
        "Costa Rica": "哥斯达黎加",
        "Croatia": "克罗地亚",
        "Cuba": "古巴",
        "Cyprus": "塞浦路斯",
        "Czech Republic": "捷克共和国",
        "Denmark": "丹麦",
        "Djibouti": "吉布提",
        "Dominica": "多米尼加",
        "Dominican Republic": "多明尼加共和国",
        "Ecuador": "厄瓜多尔",
        "Egypt": "埃及",
        "El Salvador": "萨尔瓦多",
        "Equatorial Guinea": "赤道几内亚",
        "Eritrea": "厄立特里亚",
        "Estonia": "爱沙尼亚",
        "Ethiopia": "埃塞俄比亚",
        "Falkland Islands": "福克兰群岛",
        "Faroe Islands": "法罗群岛",
        "Fiji": "斐济",
        "Finland": "芬兰",
        "France": "法国",
        "French Guiana": "法属圭亚那",
        "French Southern and Antarctic Lands": "法属南半球和南极领地",
        "Gabon": "加蓬",
        "Gambia": "冈比亚",
        "Gaza Strip": "加沙",
        "Georgia": "格鲁吉亚",
        "Germany": "德国",
        "Ghana": "加纳",
        "Greece": "希腊",
        "Greenland": "格陵兰",
        "Grenada": "格林纳达",
        "Guadeloupe": "瓜德罗普",
        "Guatemala": "危地马拉",
        "Guinea": "几内亚",
        "Guinea Bissau": "几内亚比绍",
        "Guyana": "圭亚那",
        "Haiti": "海地",
        "Honduras": "洪都拉斯",
        "Hong Kong": "香港",
        "Hungary": "匈牙利",
        "Iceland": "冰岛",
        "India": "印度",
        "Indonesia": "印尼",
        "Iran": "伊朗",
        "Iraq": "伊拉克",
        "Iraq-Saudi Arabia Neutral Zone": "伊拉克阿拉伯中立区",
        "Ireland": "爱尔兰",
        "Isle of Man": "马恩岛",
        "Israel": "以色列",
        "Italy": "意大利",
        "Ivory Coast": "科特迪瓦",
        "Jamaica": "牙买加",
        "Jan Mayen": "扬马延岛",
        "Japan": "日本",
        "Jordan": "约旦",
        "Kazakhstan": "哈萨克斯坦",
        "Kenya": "肯尼亚",
        "Kerguelen": "凯尔盖朗群岛",
        "Kiribati": "基里巴斯",
        "North Korea": "北朝鲜",
        "South Korea": "韩国",
        "Kuwait": "科威特",
        "Kyrgyzstan": "吉尔吉斯斯坦",
        "Lao PDR": "老挝",
        "Latvia": "拉脱维亚",
        "Lebanon": "黎巴嫩",
        "Lesotho": "莱索托",
        "Liberia": "利比里亚",
        "Libya": "利比亚",
        "Liechtenstein": "列支敦士登",
        "Lithuania": "立陶宛",
        "Luxembourg": "卢森堡",
        "Macau": "澳门",
        "Macedonia": "马其顿",
        "Madagascar": "马达加斯加",
        "Malawi": "马拉维",
        "Malaysia": "马来西亚",
        "Maldives": "马尔代夫",
        "Mali": "马里",
        "Malta": "马耳他",
        "Martinique": "马提尼克",
        "Mauritania": "毛里塔尼亚",
        "Mauritius": "毛里求斯",
        "Mexico": "墨西哥",
        "Moldova": "摩尔多瓦",
        "Monaco": "摩纳哥",
        "Mongolia": "蒙古",
        "Morocco": "摩洛哥",
        "Mozambique": "莫桑比克",
        "Myanmar": "缅甸",
        "Namibia": "纳米比亚",
        "Nepal": "尼泊尔",
        "Netherlands": "荷兰",
        "New Caledonia": "新喀里多尼亚",
        "New Zealand": "新西兰",
        "Nicaragua": "尼加拉瓜",
        "Niger": "尼日尔",
        "Nigeria": "尼日利亚",
        "Northern Mariana Islands": "北马里亚纳群岛",
        "Norway": "挪威",
        "Oman": "阿曼",
        "Pakistan": "巴基斯坦",
        "Panama": "巴拿马",
        "Papua New Guinea": "巴布亚新几内亚",
        "Paraguay": "巴拉圭",
        "Peru": "秘鲁",
        "Philippines": "菲律宾",
        "Poland": "波兰",
        "Portugal": "葡萄牙",
        "Puerto Rico": "波多黎各",
        "Qatar": "卡塔尔",
        "Reunion": "留尼旺岛",
        "Romania": "罗马尼亚",
        "Russia": "俄罗斯",
        "Rwanda": "卢旺达",
        "San Marino": "圣马力诺",
        "Sao Tome and Principe": "圣多美和普林西比",
        "Saudi Arabia": "沙特阿拉伯",
        "Senegal": "塞内加尔",
        "Seychelles": "塞舌尔",
        "Sierra Leone": "塞拉利昂",
        "Singapore": "新加坡",
        "Slovakia": "斯洛伐克",
        "Slovenia": "斯洛文尼亚",
        "Solomon Islands": "所罗门群岛",
        "Somalia": "索马里",
        "S. Sudan": "苏丹",
        "South Africa": "南非",
        "Spain": "西班牙",
        "Sri Lanka": "斯里兰卡",
        "St. Christopher-Nevis": "圣",
        "St. Lucia": "圣露西亚",
        "St. Vincent and the Grenadines": "圣文森特和格林纳丁斯",
        "Sudan": "苏丹",
        "Suriname": "苏里南",
        "Svalbard": "斯瓦尔巴特群岛",
        "Swaziland": "斯威士兰",
        "Sweden": "瑞典",
        "Switzerland": "瑞士",
        "Syria": "叙利亚",
        "Taiwan": "台湾",
        "Tajikistan": "塔吉克斯坦",
        "United Republic of Tanzania": "坦桑尼亚",
        "Thailand": "泰国",
        "Togo": "多哥",
        "Tonga": "汤加",
        "Trinidad and Tobago": "特里尼达和多巴哥",
        "Tunisia": "突尼斯",
        "Turkey": "土耳其",
        "Turkmenistan": "土库曼斯坦",
        "Tanzania": "坦桑尼亚",
        "Turks and Caicos Islands": "特克斯和凯科斯群岛",
        "Uganda": "乌干达",
        "Ukraine": "乌克兰",
        "United Arab Emirates": "阿联酋",
        "United Kingdom": "英国",
        "United States": "美国",
        "Uruguay": "乌拉圭",
        "Uzbekistan": "乌兹别克斯坦",
        "Vanuatu": "瓦努阿图",
        "Venezuela": "委内瑞拉",
        "Vietnam": "越南",
        "Western Sahara": "西撒哈拉",
        "Western Samoa": "西萨摩亚",
        "Yemen": "也门",
        "Yugoslavia": "南斯拉夫",
        "Democratic Republic of the Congo": "刚果民主共和国",
        "Zambia": "赞比亚",
        "Zimbabwe": "津巴布韦",
        "South Sudan": "南苏丹",
        "Somaliland": "索马里兰",
        "Montenegro": "黑山",
        "Kosovo": "科索沃",
        "Republic of Serbia": "塞尔维亚",
      },
      chinaData:[],
    };
  },
  created() {
      requestAxiosPost(apiUrl + "/data/map",{type:1}).then((res) => {
           if (res.code == 200) {
               this.chinaData=res.data
            } else {
             this.$message.error(res.msg);
            }
         });
    
    //   循环注册地图
    for (let index in this.jsonMap) {
      this.$echarts.registerMap(index, this.jsonMap[index]);
    }
  },
  mounted() {
    this.getdata()
  },
  //   更新数据
  watch: {
    value(val) {
     return myChartss.setOption(this.chinaConfigure(val));
    },
    echartsmap(){
      return myChartss.setOption(this.chinaConfigure(this.value));
    },
    chinaData(){
       return myChartss.setOption(this.chinaConfigure(this.value));
    }
  },

  methods: {
    // 点击地图外返回全球地图
  
    wwww() {
      if (this.value == "中国") {
        this.value = "world";
      
        this.chinaConfigure(this.value);
            this.BUS.$emit("paramname", this.echartsmap);
      }
    },

    chinaConfigure(area) {
      
       var worldData=  this.echartsmap

       console.log(worldData.length)
        var maxnum = 0;
        worldData.forEach(res=>{
         maxnum = maxnum> res.value ? maxnum : res.value;
       })
       if( area=='中国' ){
          this.chinaData.forEach(res=>{
            maxnum = maxnum> res.value ? maxnum : res.value;
           })
       }else{
           worldData.forEach(res=>{
            maxnum = maxnum> res.value ? maxnum : res.value;
           })
       }
     return {
        // 进行相关配置
        tooltip: {
            trigger: "item",
            formatter: function (params) {
              var value = params.value || 0;
              return params.name  ? params.name + " : " + value + "个" : '';
           },
        }, // 鼠标移到图里面的浮动提示框
        visualMap: {
          pieces: [
            {
               lte: maxnum,
              gte:  (maxnum/3*2),
        
              color: "#d51010",
            },
            {
              lte: (maxnum/3*2),
              gte: (maxnum/3),
           
              color: "#ffd6d3",
            },
            {
              lte: (maxnum/3),
              gte: 0,
              color: "#ffa491",
            }, 
            {
              lte: 0,
              gte: 0,
              color: "#f2effb",
            },
          ],
          //  color: "#f2effb",
          
          show: false,
        },

        series: [
          {
            type: "map",
            map: area,
            data:
              area == "world"
                ? worldData
                : area == "中国"
                ? this.chinaData
                : this.chinaData,
            nameMap: area == "world" ? this.nameMap : {},
            layoutCenter: ["50%", "50%"],
            layoutSize: area == "world" ? "140%" : "120%",
            zoom: 1,
            roam: false,
             label: {
               show: area == "world" ? false : true,
               color: "black",
              },
            itemStyle: {
              normal: {
                areaColor: "#f2effb",
                borderColor: "#a5b1eb",
                borderWidth: 1,
                // borderWidth: 1,
              },
             
              emphasis: {
                areaColor: {
                  type: "radial",
                  x: 0.5,
                  y: 0.5,
                  r: 0.8,
                  colorStops: [
                    // {
                    //   offset: 0,
                    //   color: "#BCBCBC", // 0% 处的颜色
                    // },
                    {
                      offset: 1,
                      color: "#fbf042", // 100% 处的颜色
                    },
                  ],
                  globalCoord: false, // 缺省为 false
                },
                // color: "#fbf042",
                // // shadowColor: "#fbf042",
                // color: 10,
                label: {
                  show: true,
                  color: "black",
                },
              },
            },
          },
        ],
      };
   
    },
    getdata(){
      this.$nextTick(()=>{
     myChartss = echarts.init(document.getElementById("map")); //这里是为了获得容器所在位置
      myChartss.setOption(this.chinaConfigure(this.value));
        window.addEventListener("resize", function () {
         myChartss.resize();
        });  
      myChartss.on("click", (params) => {
        params.event.stop(); //阻止默认事件
        if (params.name == "中国") {
           this.value = params.name;
               this.BUS.$emit("paramname", this.chinaData);     
        }
      });
      })
    }
  },
};
 
</script>
<style scoped>
.hello {
  width: 100%;
  height: 100%;
}
</style>

```




