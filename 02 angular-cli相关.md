### angular-cli构建项目

###### 1. 前置准备

```
node 和 npm 是否安装？  需要更新至一定版本 (验证： node -v; npm -v)
```

###### 2.  安装angular-cli

```
1. 全局安装 $ npm install -g @angular/cli (验证：$ ng version)
```

###### 3. 生成一个新项目

```
1. $ ng new my-app 或 $ ng new my-app --routing; --routing 为参数，还有很多其它的参数
```

###### 4. 运行该项目

```
1. $ cd my-app
2. $ ng serve / $ npm run start / $ npm start
	Angular CLI 从 .angular-cli.json 文件中加载配置信息
	Angular CLI 运行 Webpack 打包相关 JavaScript 和 CSS 文件
	Angular CLI 启动 webpack dev server 本地开发服务器，默认的地址是 localhost:4200
3. 如果报错，需要 npm install 下载各种包
```

###### 5. 安装第三方插件

```
1. npm install jquery -S            第三方插件    (生产环境Dependencies)
2. npm install @types/jquery -D     类型描述文件  (开发环境DevDependencies)
3. angular-cli.json 文件 script/style对象中添加相应的路径
```

###### 6. 添加新功能

```
ng g class my-new-class: 新建 class
ng g component my-new-component: 新建组件
ng g directive my-new-directive: 新建指令
ng g enum my-new-enum: 新建枚举
ng g module my-new-module: 新建模块
ng g pipe my-new-pipe: 新建管道
ng g service my-new-service: 新建服务
```







- 无序列表：输入-之后输入空格
- 有序列表：输入数字+“.”之后输入空格
- [###个数 代表 h1-h6]
- 标题：ctrl+数字    *// h1&h2有下划线*
- 表格：ctrl+t
- 搜索：ctrl+f
- 替换：ctrl+h
- 代码块：ctrl+alt+f
- 加粗：ctrl+b
- 倾斜：ctrl+i
- 下划线：ctrl+u
- 删除线：alt+shift+5
- 插入图片：直接拖动到指定位置即可或者ctrl+shift+i
- 插入链接：ctrl+k