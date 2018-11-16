# backupHexo
备份hexo博客

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