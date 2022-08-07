---
title: vscode
date: 2022-06-18 15:40:50
categories: [vscode]
tags:
- vscode
- extension
---

# VS Code安装

官网：[Visual Studio Code](https://code.visualstudio.com/)

vscode又两种版本（user版和system版），默认下载user版。（其他下载中可选择版本，具体区别不明）

安装时勾选上两个`通过Code打开`（可以右键文件或文件夹在vscode中打开）

# 推荐扩展

- Chinese (Simplified)  //适用于 VS Code 的中文（简体）语言包
- Code Spell Checker  //拼写检查
- ESLint  //ESLint支持
- git-commit-plugin  //Git Commit模板
- GitLens — Git supercharged  //Git历史记录
- Google Translate  //划词翻译
- IntelliCode  //代码提示
- Live Server  //实时加载的本地服务器
- markdownlint  //markdown格式化
- Material Icon Theme  //图标主题
- open in browser  //右键在浏览器中打开
- Prettier - Code formatter  //Prettier格式化
- Tabnine AI Autocomplete  //代码快速生成
- Vue Language Features (Volar)  //Vue支持

# 修改扩展安装位置

默认安装位置：`C:\Users\{用户名}\.vscode\extensions`

将`extensions`文件夹剪切至其他盘（比如：`D:\Microsoft VS Code\extensions`）

管理员身份运行cmd

```
mklink /D "C:\Users\{用户名}\.vscode\extensions" "D:\Microsoft VS Code\extensions"
```

