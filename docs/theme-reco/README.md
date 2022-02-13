---
title: Vue常见的坑
date: 2020-05-29
---
- 遇到Echarts 数据不渲染 注意 不要忘记 this.nextTick 
```
   methods: {
       changeMsg(){
            this.msg1='我喜欢vue的简洁';
            //this.msg2=this.msg3=this.$refs.msg.innerHTML
            this.$nextTick(()=>{
                 this.msg2=this.msg3=this.$refs.msg.innerHTML
            })
        }
    },
```
---
 keepalive 页面缓存
---



