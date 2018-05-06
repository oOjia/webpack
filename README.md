## 1. 什么是WebPack
WebPack可以看做是模块打包机：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其打包为合适的格式以供浏览器使用。

构建就是把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容。

- 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
- 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
- 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
- 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
- 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

构建其实是工程化、自动化思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。 构建给前端开发注入了更大的活力，解放了我们的生产力。

## 2. 初始化项目
```js
mkdir webpack-start
cd webpack-start
npm init
```

## 3. 快速上手
### 3.1 webpack核心概念
- Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
- Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
- Chunk：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
- Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
- Plugin：扩展插件，在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。
- Output：输出结果，在 Webpack 经过一系列处理并得出最终想要的代码后输出结果。



### 3.2 配置webpack
```js
npm install webpack webpack-cli -D
```
- 创建src
- 创建dist
  - 创建index.html
- 配置文件webpack.config.js
  - entry：配置入口文件的地址
  - output：配置出口文件的地址
  - module：配置模块,主要用来配置不同文件的加载器
  - plugins：配置插件
  - devServer：配置开发服务器


## 4. 配置开发服务器
```js
npm i webpack-dev-server –D
```

- contentBase 配置开发服务运行时的文件根目录
- host：开发服务器监听的主机地址
- compress 开发服务器是否启动gzip等压缩
- port：开发服务器监听的端口

```diff
+ devServer:{
+        contentBase:path.resolve(__dirname,'dist'),
+        host:'localhost',
+        compress:true,
+        port:8080
+ }
```
```diff
+  "scripts": {
+    "build": "webpack --mode development",
+    "dev": "webpack-dev-server --open --mode development "
+  }
```

## 5. 支持加载css文件
### 5.1 什么是Loader
通过使用不同的Loader，Webpack可以要把不同的文件都转成JS文件,比如CSS、ES6/7、JSX等

- test：匹配处理文件的扩展名的正则表达式
- use：loader名称，就是你要使用模块的名称
- include/exclude:手动指定必须处理的文件夹或屏蔽不需要处理的文件夹
- query：为loaders提供额外的设置选项

loader三种写法
- use
- loader
- use+loader

### 5.2 css-loader
```js
npm i style-loader css-loader -D
```
配置加载器
```diff
    module: {
+        rules:[
+            {
+                test:/\.css$/,
+                use:['style-loader','css-loader'],
+                include:path.join(__dirname,'./src'),
+                exclude:/node_modules/
+            }
+        ]
    },
```

## 6. 自动产出html
我们希望自动能产出HTML文件，并在里面引入产出后的资源
```js
npm i html-webpack-plugin -D
```
- minify 是对html文件进行压缩，removeAttrubuteQuotes是去掉属性的双引号
- hash 引入产出资源的时候加上哈希避免缓存
- template 模版路径

```diff
    plugins: [
+        new HtmlWebpackPlugin({
+       minify: {
+            removeAttributeQuotes:true
+        },
+        hash: true,
+        template: './src/index.html',
+        filename:'index.html'
    })]
```

## 7. 支持图片
### 7.1 手动添加图片
```js
npm i file-loader url-loader -D
```
- [file-loader](http://npmjs.com/package/file-loader) 解决CSS等文件中的引入图片路径问题
- [url-loader](https://www.npmjs.com/package/url-loader) 当图片较小的时候会把图片BASE64编码，大于limit参数的时候还是使用file-loader 进行拷贝

```js
let logo=require('./images/logo.png');
let img=new Image();
img.src=logo;
document.body.appendChild(img);
```

```js
  {
    test:/\.(jpg|png|gif|svg)$/,
    use:'url-loader',
    include:path.join(__dirname,'./src'),
    exclude:/node_modules/
  }
```

### 7.2 在CSS中引入图片
还可以在CSS文件中引入图片
```css
.img-bg{
    background: url(./images/logo.png);
    width:173px;
    height:66px;
}
```

## 8. 分离CSS
因为CSS的下载和JS可以并行,当一个HTML文件很大的时候，我们可以把CSS单独提取出来加载

```js
npm install --save-dev extract-text-webpack-plugin@next
```

```diff
{
                test:/\.css$/,
+                use: ExtractTextWebpackPlugin.extract({
+                    use:'css-loader'
+                }),
                include:path.join(__dirname,'./src'),
                exclude:/node_modules/
            },

   plugins: [
+        new ExtractTextWebpackPlugin('css/index.css'),
```

