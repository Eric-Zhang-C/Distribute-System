* git push origin master
    *   https://www.cnblogs.com/fatt/p/6296605.html
* git commit   
    * https://blog.csdn.net/qianxuedegushi/article/details/80311358
* Mac下，git忽略.DS_Store文件
    * https://blog.csdn.net/nunchakushuang/article/details/50511765
* 在mac终端中使用git
    * https://www.jianshu.com/p/38ce605942b4
    * https://blog.csdn.net/xidiancoder/article/details/71273768
* [官方文档]git远程仓库的使用
    * https://git-scm.com/book/zh/v2/Git-基础-远程仓库的使用
* ssh-add
    * https://blog.csdn.net/dzhshf/article/details/80870759
```
zyhs-MacBook-Pro:lab4 bian$ git push
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
zyhs-MacBook-Pro:lab4 bian$ ssh-add -K /Users/bian/.ssh/github_rsa
Identity added: /Users/bian/.ssh/github_rsa (315889187@qq.com)
```
