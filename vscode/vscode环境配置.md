[ssh登录linux](https://www.cnblogs.com/WindSun/p/12142621.html)



# 常见变量

- `${workspaceFolder}`: 在VS Code中打开的文件夹的绝对路径
- `${workspaceRootFolderName}`或者`${workspaceFolderBasename}`: 表示workspace的文件夹名，不带任何斜杠（/）
- `${file}`: 当前打开的文件的绝对路径
- `${relativeFile}`:文件在workspace中的相对路径
- `${fileBasename}`:当前文件的文件名
- `${fileBasenameNoExtension}`:当前文件的文件名，不带后缀
- `${fileExtname}`:当前文件的后缀
- `${fileDirname}`:文件所在的文件夹绝对路径
- `${lineNumber}`:当前文件光标所在的行号
- `${env:PATH}`:系统中的环境变量


# 路径配置

## 路径中`/** /* /?`的含义
- `/**`匹配任意多级路径如`/aa/bb/cc`
- `/*` 仅可匹配一级路径如`/aa`
- `/?` 仅可匹配一级路径且最多只有一个字符如`/a`

## compilerPath
- `g++.exe`所在的路径
- 例如：`"C:\\mingw64\\bin\\g++.exe"`
- 配置完这个后，和系统有关的头文件都能找到了

## includePath
- 

