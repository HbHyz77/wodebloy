---
title: H5移动端布局问题解决
date: 2021-12-20
---
##  1.自适应布局两种方案
   - <font  size=3>响应式布局</font> --<font  color=#4359ff>原声js</font>
```
 //首先是一个立即执行函数，执行时传入的参数是window和document
(function flexible (window, document) {
    var docEl = document.documentElement // 返回文档的root元素
    var dpr = window.devicePixelRatio || 1 
    // 获取设备的dpr，即当前设置下物理像素与虚拟像素的比值
  
    // 调整body标签的fontSize，fontSize = (12 * dpr) + 'px'
    // 设置默认字体大小，默认的字体大小继承自body
    function setBodyFontSize () {
      if (document.body) {
        document.body.style.fontSize = (12 * dpr) + 'px'
      } else {
        document.addEventListener('DOMContentLoaded', setBodyFontSize)
      }
    }
    setBodyFontSize();
    // set 1rem = viewWidth / 10   1rem =37.5px
    // 设置root元素的fontSize = 其clientWidth / 10 + ‘px’
    function setRemUnit () {
      var rem = docEl.clientWidth / 10
      docEl.style.fontSize = rem + 'px'
    }
    setRemUnit()
      // 当我们页面尺寸大小发生变化的时候，要重新设置下rem 的大小
      window.addEventListener('resize', setRemUnit)
          // pageshow 是我们重新加载页面触发的事件
      window.addEventListener('pageshow', function(e) {
          // e.persisted 返回的是true 就是说如果这个页面是从缓存取过来的页面，也需要从新计算一下rem 的大小
          if (e.persisted) {
              setRemUnit()
          }
      })
  
    // 检测0.5px的支持，支持则root元素的class中有hairlines
    if (dpr >= 2) {
      var fakeBody = document.createElement('body')
      var testElement = document.createElement('div')
      testElement.style.border = '.5px solid transparent'
      fakeBody.appendChild(testElement)
      docEl.appendChild(fakeBody)
      if (testElement.offsetHeight === 1) {
        docEl.classList.add('hairlines')
      }
      docEl.removeChild(fakeBody)
    }
  }(window, document))
  ```
  ---
  ## Vue响应式
  - 下载安装包 下面的安装包
  ```
  npm install postcss-import postcss-loader postcss-px-to-viewport --save-dev
  npm i postcss-px-to-viewport-opt
  ```
  ```
   module.exports = {
    plugins: {
        "autoprefixer": {
            path: ['./src/*']
        },
        "postcss-import": {},
        "postcss-px-to-viewport-opt": {
            "viewportWidth": "1920", //视窗的宽度，对应的是我们设计稿的宽度
            "viewportHeight": "1080", // 视窗的高度
            "unitPrecision": 2, //指定`px`转换为视窗单位值的小数位数(很多时候无法整除)
            "viewportUnit": "vw", //指定需要转换成的视窗单位，建议使用vw
            "selectorBlackList": ['#nprogress'], //指定不转换为视窗单位的类
            "minPixelValue": 1, // 小于或等于`1px`不转换为视窗单位
            "mediaQuery": false, // 允许在媒体查询中转换`px`
            // "exclude": /(\/|\\)(node_modules)(\/|\\)/
        },
    }
};