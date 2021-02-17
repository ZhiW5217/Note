###集中式(svn)
    svn因为每次存的都是差异需要的硬盘空间会相对的小一点可是回滚的速度会很慢
    优点:
        代码存放在单一的服务器上便于项目的管理
    缺点:W
        服务器宕机:员工写的代码得不到保障
        服务器炸了:整个项目的历史记录都会丢失
###分布式(git)
    git每次存的都是项目的完整快需要的硬盘空间会相对大一点
        (Git团队对代码做了极致的压缩最终需要的实际空间比svn多不了太多可是Git的回滚速度极快)
        
        
### 安装
    https://git-scm.com/download/win
    git --version
    
### 初始化
    $ git config --global user.name "zzw"
    $ git config --global user.email xxxx@xxx.xx
    $ git config --list
    
### 区域
    工作区
    暂存区
    版本库

### 对象
    Git对象
        key:val 组成的键值对
                键值对在git内部是一个blob类型
    树对象
    提交对象