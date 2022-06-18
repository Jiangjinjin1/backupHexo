# backupHexo
备份hexo博客

Using
```shell
hexo backup 
```
or
```shell
hexo b
```

## 另外还有其他几个常用命令：
```javascript
$ hexo new "postName" #新建文章
$ hexo new page "pageName" #新建页面
```

## 常用简写
```javascript
$ hexo n == hexo new
$ hexo g == hexo generate
$ hexo s == hexo server
$ hexo d == hexo deploy
```


## 常用组合
```javascript
$ hexo d -g #生成部署
$ hexo s -g #生成预览
```


## 如何备份
下载hexo-git-backup依赖,
在_.config.yml添加backup:配置具体如下

备份整个hexo
```
backup:
  type: git
  message: update 添加readme `这里是每次提交时填写的commit信息`
  repository:
     github: git@github.com:Jiangjinjin1/backupHexo.git,master
     gitcafe: git@github.com:Jiangjinjin1/backupHexo.git,master
 ```
 
 ## 提交备份
 改完代码，在message填写commit内容信息，执行hexo backup 或者hexo b即可上传
