# coffee 

🌠灵感如同流星 如果没有抓住 就只能等下一颗

# init 🚲

```
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/Likang00/coffee.git
git push -u origin master
```

# start 🛵

## install-node.js
1.安装node.js
http://nodejs.cn/

2.更换npm源 并验证

``` shell
npm config set registry https://registry.npm.taobao.org
npm config get registry
```

3.安装hexo

``` shell
npm install -g hexo-cli
```

## install-themes
1. 在 **hexo** 的根目录执行

``` shell
git clone -b master https://github.com/Longlongyu/hexo-theme-Cxo themes/Cxo
```

2. 修改 **hexo** 的根目录下的 `_config.yml` 的 `theme` 字段为 `Cxo`

``` yaml
theme: Cxo
```

3. 如果你没有安装 `jade renderer` `sass renderer`,你还要执行

``` shell
npm install hexo-renderer-jade hexo-renderer-sass --save
```

## npm-command 💻

####  npm-audit 命令
文档: https://docs.npmjs.com/cli/audit

``` shell 
npm audit： npm@5.10.0 & npm@6，允许开发人员分析复杂的代码，并查明特定的漏洞和缺陷。

npm audit fix：npm@6.1.0,  检测项目依赖中的漏洞并自动安装需要更新的有漏洞的依赖，而不必再自己进行跟踪和修复。
```

#### npm 简单命令

``` shell 
npm install 本地安装 
npm install -g 全局安装
npm list 查看本地安装的模块
npm -g list 查看全局安装的模块
```

---

likang00@2019

---