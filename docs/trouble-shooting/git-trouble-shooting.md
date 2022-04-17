## 无法连接

> 直接改.gitconfig文件

## OpenSSL SSL_read: Connection was reset, errno 10054 错误解决

```
git config --global http.sslVerify "false"
```

## [终端改为git](https://code.visualstudio.com/docs/editor/integrated-terminal#_configuration)

**在settings.json中添加** 
```
{
"terminal.integrated.profiles.windows": {
        "Git-Bash": {
            "path": "D:\\Git\\bin\\bash.exe",
            "args": []
        }
    }
}
```

?> terminal.integrated.Default Profile.windows设置默认终端

## 重装完git，vs code 找不到存储库

> 重启gitlen插件，如果不行，查看如下设置

**settings.json**
```
{
"git.path": "D:\\Git\\bin\\bash.exe"
}
```

## .gitignore无法生效

[解决.gitignore无法生效的问题](https://www.jianshu.com/p/ddd248164a5d?utm_medium=reader_share&utm_content=note)  
刷新gitlen插件源代码更改管理