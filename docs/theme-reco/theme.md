---
title: 前端三大框架
date: 2020-05-27
---
```
正册
// 验证账号
export const isAccount = (val) => {
    return /^[\u4E00-\u9FA5a-zA-Z0-9]{3,12}$/.test(val)
}

// 验证密码
export const isPassword = (val) => {
    return /^[a-zA-Z0-9_-]{6,12}$/.test(val)
}