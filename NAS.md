
## Current config in server
`/mnt/hdd1 *(rw,sync,no_subtree_check,all_squash,anonuid=0,anongid=0,insecure,no_root_squash)`
* `anonuid=0,anongid=0`: root user, can successfully create new files


## some good turorials
* https://blog.csdn.net/donghaixiaolongwang/article/details/79230220
* [Linux 下 NFS 服务权限问题解决 Permission denied](https://i-cooltea.github.io/posts/linux-%E4%B8%8B-nfs-%E6%9C%8D%E5%8A%A1%E6%9D%83%E9%99%90%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3-permission-denied/)
  * 有服务端 uid$gid 为 0 即 root 用户的权限。

* [Linux 开启 NFS 服务，把你的linux变成NAS](https://yucanlin.wordpress.com/2016/01/07/linux-%E5%BC%80%E5%90%AF-nfs-%E6%9C%8D%E5%8A%A1%EF%BC%8C%E6%8A%8A%E4%BD%A0%E7%9A%84linux%E5%8F%98%E6%88%90nas/)
