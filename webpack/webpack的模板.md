## * webpack
   ### 1.什么是Webpack？
   #### WebPack可以看做是模块打包机：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。

   ### 2.为什要使用WebPack？
   #### 现今的很多网页其实可以看做是功能丰富的应用，它们拥有着复杂的JavaScript代码和一大堆依赖包。为了简化开发的复杂度，前端社区涌现出了很多好的实践方法

   ### 3.WebPack和Gulp相比有什么特性？
   #### 其实Webpack和另外两个并没有太多的可比性，Gulp是一种能够优化前端的开发流程的工具，而WebPack是一种模块化的解决方案，不过Webpack的优点使得Webpack在很多场景下可以替代Gulp/Grunt类的工具。
   #### Gulp的工作方式是：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，工具之后可以自动替你完成这些任务。
   #### Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件。
   #### 如果实在要把二者进行比较，Webpack的处理速度更快更直接，能打包更多不同类型的文件。

```js
const webpack = require("webpack") // webpack
const UglifyjsWebpackPlugin = require("uglifyjs-webpack-plugin") // 压缩js模块
const HtmlWebpackPlugin = require("html-webpack-plugin") // 打包html模块
module.exports = {
    // 打包js的入口
    entry: // ["./entry1.js", "./entry2.js"] // 单出口的写法
    { // 多出口的写法
        module1: "./entry1.js",
        module2: "./entry2.js"
    },
    // 打包js的出口
    output: {
        path: __dirname + "/dist",
        filename: "[name].js", // 多出口
        // filename: "bundle.js", // 单出口
    },
    // 插件
    plugins: [
        // 热替换插件
        new webpack.HotModuleReplacementPlugin(),
        // 压缩js
        new UglifyjsWebpackPlugin(),
        // 打包html
        new HtmlWebpackPlugin({
            filename: "index.html",
            path: __dirname + "/dist",
            template: "./index.html",
            // 多入口多出口的依赖顺序 => sort函数
            chunksSortMode: function (chunk1, chunk2) {
                var order = ["module1", "module2"]
                var order1 = order.indexOf(chunk1.names[0])
                var order2 = order.indexOf(chunk2.names[0])
                return order1 - order2
            },
            // 压缩html
            minify: {
                minifyCSS: true, // 压缩css
                minifyJS: true, // 压缩js
                removeComments: true, // 清除注释
                collapseWhitespace: true, // 压缩HTML
                collapseBooleanAttributes: true, // 省略布尔属性的值 <input checked='true'/> ==> <input />
                removeEmptyAttributes: true, // 删除所有空格作属性值 <input id='' /> ==> <input />
                removeScriptTypeAttributes: true, // 删除<script>的type='text/javascript'
                removeStyleLinkTypeAttributes: true, // 删除<style>和<link>的type='text/css'
            }
        })
    ],
    // 模块
    module: {
        rules: [
            { test: /\.js$/, use: ["babel-loader"] }, // es6 转 es5
            // babel-loader & babel-core & babel-preset-env
            // touch .babelrc  =>  {"presets":["ebv"]}
            { test: /\.css$/, use: ["style-loader", "css-loader"] },
            // css-loader 为了使得webpack能识别.css 结尾的 文件
            // style-loader 为了把 js 形式 css 能够正常在html 中有效
            { test: /\.html$/, use: ["html-loader"] }, // 使得webpack能识别html
            { test: /\.(png|jpg|gif)$/, use: ["url-loader"] } // 加载图片的 内部含有 file-loader
        ]
    },
    // 热替换
    devServer: {
        port: 8080,  // devServer 本地服务端口号
        open: 'http://localhost:8080', // 自动打开浏览器窗口
        contentBase: __dirname // 服务器根目录
    },
    // 打包速度
    devtool: "eval",
    // 开发环境推荐 eval (速度快)
    // 生产环境推荐 source-map (完整的source-map)
}
```