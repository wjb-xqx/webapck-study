# 学习webpack打包

********************************************************
#webpack最基本使用
# 安装webpack前提安装node.js环境，是基于node环境下的 
1.npm i webpack -g  
npm i webpack-cli -g
或者
2.npm install webpack –save-dev 
npm install webpack-cli –save-dev
# 初步使用webpack打包构建
1.`npm init -y`初始化项目 如果项目是中文不能-y ，输入npm init 进行设置
2.将所需要执行的代码防止main.js中
3.执行webpack ./src/main.js -o ./dist/bundle.js（将m输入main.js中代码，输出到指定的地方bundle.js. (名称可以随意取，一般用bundle即可)---
   
4.在index.js中引入<script src="../dist/bundle.js"></script>，js代码就可以生效

代码解析
index.html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../dist/bundle.js"></script>
</head>
<body>
    <ul>
        <li>这是第1个li</li>
        <li>这是第2个li</li>
        <li>这是第3个li</li>
        <li>这是第4个li</li>
        <li>这是第5个li</li>
        <li>这是第6个li</li>
    </ul>
</body>

main.js
cnpm i jquery -S
import $ from 'jquery'

$(function () {
    $('li:odd').css('color','red')
})

总结：
webpack能够处理js文件的互相依赖关系
webpack会将高级语法降低让每个浏览器都能读取（处理js兼容问题）

********************************************************
# 再基础上进行优化1
每次更爱数据都需要输入入口跟出口文件webpack ./src/main.js -o ./dist/bundle.js，
我们现在想只需要输入webpack就可以执行，则创建文件  webpack.config.js，来指定出口入口文件
webpack.config.js中代码
const path = require('path')
module.exports = {
    entry:path.join(__dirname,'./src/main.js'),//入口文件
    output:{
        path:path.join(__dirname,'./dist'),//指定打包好的文件，输出到哪个目录中去
        filename:'bundle.js' //这是指定输出的文件名称
    }
}

然后直接输入webpack即可执行代码

当我们在 控制台，直接输入 webpack 命令执行的时候，webpack 做了以下几步：
1. 首先，webpack 发现，我们并没有通过命令的形式，给它指定入口和出口
2. webpack 就会去 项目的 根目录中，查找一个叫做 `webpack.config.js` 的配置文件
3. 当找到配置文件后，webpack 会去解析执行这个 配置文件，当解析执行完配置文件后，就得到了 配置文件中，导出的配置对象
4. 当 webpack 拿到 配置对象后，就拿到了 配置对象中，指定的 入口  和 出口，然后进行打包构建；


********************************************************
# 再基础上进行优化2
保存，自动打包编译
1.运行`cnpm i webpack-dev-server -D`安装到开发依赖
2.`package.json`文件中的指令，来进行运行`webpack-dev-server`命令，
在`scripts`节点下新增`"dev": "webpack-dev-server"`指令---此时输出npm run dev 启动的`http://localhost:8080/`网站，发现是一个文件夹的面板，需要点击到src目录下，才能打开我们的index首页，
并且修改代码后不会更改，因为读取的bundle路径是根路径<script src="/bundle.js"></script>
3.

********************************************************
# 再基础上进行优化3
自动打包完，打开浏览器
在package.json添加
"dev2": "webpack-dev-server --open,

自动设置端口
 "dev": "webpack-dev-server --open --port 3000 " 

直接进入src的首页
"dev": "webpack-dev-server --open --port 3000 --contentBase src"

保存自动局部更新数据
"dev": "webpack-dev-server --open --port 3000 --contentBase src --hot"
# 生产内存中页面
因为bunlle是物理自盘中的页面
1.cnpm i html-webpack-plugin -D
2.在webpack.config.js中添加
const webpack = require('webpack')
// 导入在内存中生成 HTML 页面的 插件
// 只要是插件，都一定要 放到 plugins 节点中去
// 这个插件的两个作用：
//  1. 自动在内存中根据指定页面生成一个内存的页面
//  2. 自动，把打包好的 bundle.js 追加到页面中去
const htmlWebpackPlugin = require('html-webpack-plugin')

plugins: [ // 配置插件的节点
    new webpack.HotModuleReplacementPlugin(), // new 一个热更新的 模块对象， 这是 启用热更新的第 3 步
    new htmlWebpackPlugin({ // 创建一个 在内存中 生成 HTML  页面的插件
        template: path.join(__dirname, './src/index.html'), // 指定 模板页面，将来会根据指定的页面路径，去生成内存中的 页面
        filename: 'index.html' // 指定生成的页面的名称
    })
],

3.删除inden.html中<script src="/bundle.js"></script>，不需要手动引入
#  打包处理 css 文件
注意： webpack, 默认只能打包处理 JS 类型的文件，无法处理 其它的非 JS 类型的文件；
// 如果要处理 非JS类型的文件，我们需要手动安装一些 合适 第三方 loader 加载器；
// 1. 如果想要打包处理 css 文件，需要安装 cnpm i style-loader css-loader -D
// 2. 打开 webpack.config.js 这个配置文件，在 里面，新增一个 配置节点，叫做 module, 它是一个对象；在 这个 module 对象身上，有个 rules 属性，这个 rules 属性是个 数组；这个数组中，存放了，所有第三方文件的 匹配和 处理规则；
 { test: /\.css$/, use: ['style-loader', 'css-loader'] }

// 注意： webpack 处理第三方文件类型的过程：
// 1. 发现这个 要处理的文件不是JS文件，然后就去 配置文件中，查找有没有对应的第三方 loader 规则
// 2. 如果能找到对应的规则， 就会调用 对应的 loader 处理 这种文件类型；
// 3. 在调用loader 的时候，是从后往前调用的；
// 4. 当最后的一个 loader 调用完毕，会把 处理的结果，直接交给 webpack 进行 打包合并，最终输出到  bundle.js 中去
#  配置处理less文件
cnpm i less less-loader -D
打开 webpack.config.js 这个配置文件，在 里面，新增一个 配置节点，叫做 module, 它是一个对象；在 这个 module 对象身上，有个 rules 属性，这个 rules 属性是个 数组；这个数组中，存放了，所有第三方文件的 匹配和 处理规则；
 { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] }
#  配置处理sass文件
cnpm i sass-loader -D
cnpm i node-sass -D 
匹配
{ test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
#  处理 图片路径的 loader
   cnpm i url-loader file-loader -D
  { test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader' }, // 处理 图片路径的 loader
   
# ********************************************************
# webpack的使用步骤总结
1.安装node.js环境
2.创建项目名称，dist目录，src目录-index.html css js main.js
3.安装webpack（全局或者局部安装）
4.初始化`npm init -y`初始化项目 如果项目是中文不能-y ，输入npm init 进行设置---包管理
5.main.js 引入页面需要的js文件，css文件等，减少页面的引入，从而减少发送请求次数
6.建文件 webpack.config.js，来指定出口入口文件来管理main.js代码执行
    webpack.config.js中代码
    const path = require('path')
    module.exports = {
        entry:path.join(__dirname,'./src/main.js'),//入口文件
        output:{
            path:path.join(__dirname,'./dist'),//指定打包好的文件，输出到哪个目录中去
            filename:'bundle.js' //这是指定输出的文件名称
        }
    }
7.`cnpm i webpack-dev-server -D` 保存，自动打包编译代码
8.打包完，自动打开浏览器，自动设置端口，保存自动局部更新数据
在package.json添加
"dev": "webpack-dev-server --open --port 3000 --hot"
9.生产内存中页面
    cnpm i html-webpack-plugin -D 
    在webpack.config.js中添加
    const webpack = require('webpack')
    const htmlWebpackPlugin = require('html-webpack-plugin')
    plugins: [ // 配置插件的节点---与入口出口文件平级
        new webpack.HotModuleReplacementPlugin(),
        new htmlWebpackPlugin({ 
            template: path.join(__dirname, './src/index.html'), // 指定 模板页面，将来会根据指定的页面路径，去生成内存中的 页面
            filename: 'index.html' // 指定生成的页面的名称
        })
    ],
10.配置出js之外的文件
 打包处理 css 文件 
 cnpm i style-loader css-loader -D
 配置处理less文件
 cnpm i less less-loader -D
 配置处理sass文件
 cnpm i sass-loader -D
 cnpm i node-sass -D 

因为默认情况下，webpack无法处理css中的url地址，不管是图片还是字体库
cnpm i url-loader file-loader -D
 { test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader?limit=7631&name=[hash:8]-[name].[ext]' }, // 处理 图片路径的 loader
    // limit 给定的值，是图片的大小，单位是 byte， 如果我们引用的 图片，大于或等于给定的 limit值，则不会被转为base64格式的字符串，
      // 如果 图片小于给定的 limit 值，则会被转为 base64的字符串
处理 字体文件的 loader
 { test: /\.(ttf|eot|svg|woff|woff2)$/, use: 'url-loader' }, // 处理 字体文件的 loader 

 然后在webpack.config.js 中配置
   module: {// 这个节点，用于配置 所有 第三方模块 加载器----与入口出口平级
        rules: [ // 所有第三方模块的 匹配规则
            { test: /\.css$/, use: ['style-loader', 'css-loader'] }, //  配置处理 .css 文件的第三方loader 规则
            { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] }, //配置处理 .less 文件的第三方 loader 规则
            { test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }, // 配置处理 .scss 文件的 第三方 loader 规则
           // { test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader' }, // 处理 图片路径的 loader
             { test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader?limit=7631&name=[hash:8]-[name].[ext]' },
             { test: /\.(ttf|eot|svg|woff|woff2)$/, use: 'url-loader' },

          ]
    }


    // 注意： 如果要通过路径的形式，去引入 node_modules 中相关的文件，可以直接省略 路径前面的 node_modules 这一层目录，直接写 包的名称，然后后面跟上具体的文件路径
    // 不写 node_modules 这一层目录 ，默认 就会去 node_modules 中查找
    import 'bootstrap/dist/css/bootstrap.css'

    配置 Babel 来转换高级的ES语法
     cnpm i babel-core babel-loader babel-plugin-transform-runtime -D
     cnpm i babel-preset-env babel-preset-stage-0 -D
        { test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ }, 
            // 配置 Babel 来转换高级的ES语法
     在项目的 根目录中，新建一个 叫做 .babelrc  的Babel 配置文件，这个配置文件，属于JSON格式，所以，在写 .babelrc 配置的时候，必须符合JSON语法规范： 不能写注释，字符串必须用双引号
在 .babelrc 写如下的配置：  大家可以把 preset 翻译成 【语法】 的意思
        // {
        //   "presets": ["env", "stage-0"],
        //   "plugins": ["transform-runtime"]
        // }


11.运行npm run dev


出现webpack-dev-server不是内部文件，但又装了，重新装一下，可能曾经装过
