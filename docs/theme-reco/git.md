---
title: Git 疑难解决
date: 2021-12-21
---
- Git 提交命名
```
git status 查看状态
git init 初始化 命名 
git clone 拉取代码
git pull 更新
git add . 添加
git commit -m "随便添加"
git push origin master  提交仓库
```
- Git 项目冲突解决 
  - 第一种报错   不要慌 先看看  modified和new file  明显提示你 这两个文件没有上传成功 
```
E:\JavaDev\template_workspace\zhw-free>git status                         
On branch master                                                          
Your branch is up to date with 'origin/master'.                           
                                                                          
Changes to be committed:                                                  
  (use "git restore --staged <file>..." to unstage)                       
        modified:   zhw-free-demo/src/main/resources/application.yml      
        new file:   zhw-free-demo/src/main/resources/logback-spring.xml   
                                                                          
Changes not staged for commit:                                            
  (use "git add <file>..." to update what will be committed)              
  (use "git restore <file>..." to discard changes in working directory)   
        modified:   .gitignore   
```
<font size=4 color=#ee6688>方式一  </font>
- 在项目文件种找到这报错的文件  把他单独找个文件存放  之后删除项目中的这两个文件  先提交到仓库里面
- 之后在添加删除的文件 重新把整个文件提交上去
