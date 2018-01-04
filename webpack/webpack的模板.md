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