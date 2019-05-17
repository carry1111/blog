## 一. webpack打包配置和书写npm包

#### 1.新建一个空文件夹,进入到根目录下。执行
```javascript
npm init
```
初始化项目。然后会让你填一些项目相关的信息，跟着提示填就是了。没啥说的。注意name不要和现有的其他npm包重名了，不然一会儿发Npm包的时候会失败，可以先去npmjs.com搜一下有没有重名的。

这一步完成后，会生成package.json文件。
#### 2.为了使用es6和webpack，在devDependencies字段中，应该至少加入以下依赖：
package.json
```
{
	"name": "npm-study-hamupp",
	"version": "1.0.3",
	"description": "",
	"main": "dist/helloMsg.min.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1",
		"start": "webpack-dev-server --hot --inline",
		"build": "webpack --display-error-details --config webpack.config.js"
	},
	"repository": {
		"type": "git",
		"url": "git+https://github.com/hamuPP/npmStudy.git"
	},
	"author": "",
	"license": "ISC",
	"bugs": {
		"url": "https://github.com/hamuPP/npmStudy/issues"
	},
	"homepage": "https://github.com/hamuPP/npmStudy#readme",
	"devDependencies": {
		"babel-core": "^6.26.0",
		"babel-loader": "^7.1.2",
		"babel-plugin-transform-object-rest-spread": "^6.26.0",
		"babel-plugin-transform-runtime": "^6.23.0",
		"babel-polyfill": "^6.26.0",
		"babel-preset-es2015": "^6.24.1",
		"css-loader": "^0.28.7",
		"es6-promise": "^4.1.1",
		"less": "^2.7.3",
		"less-loader": "^4.0.5",
		"style-loader": "^0.19.0",
		"url-loader": "^0.6.2",
		"vue": "^2.5.9",
		"vue-hot-reload-api": "^2.2.4",
		"vue-html-loader": "^1.2.4",
		"vue-loader": "^13.5.0",
		"vue-router": "^3.0.1",
		"vue-style-loader": "^3.0.3",
		"vue-template-compiler": "^2.5.9",
		"vuex": "^3.0.1",
		"webpack": "^3.9.1",
		"webpack-dev-server": "^2.9.5"
	}
}
```
#### 3.执行npm install，下载这些依赖包。
#### 4.新建2个文件夹：dist和src，名字按照个人习惯吧。我习惯dist代表发布时的目录，src是开发目录。dist里面的js是到时候通过webpack打包后的文件。待会只会提交dist目录到npm官网上，src不提交。
#### 5.加入webpack打包配置，并把src中的内容打包进dist目录内：

根目录下新增weppack.config.js文件
```

const path = require("path");
const webpack = require("webpack");
const uglify = require("uglifyjs-webpack-plugin");
 
module.exports = {
    devtool: 'source-map',
    entry: "./src/index.js",//入口文件，就是上步骤的src目录下的index.js文件，
    output: {
        path: path.resolve(__dirname, './dist'),//输出路径，就是上步骤中新建的dist目录，
        publicPath: '/dist/',
        filename: 'helloMsg.min.js',
        libraryTarget: 'umd',
        umdNamedDefine: true
    },
    module: {
        rules: [{
                test: /\.vue$/,
                loader: 'vue-loader'
            },
            {
                test: /\.less$/,
                use: [
                    { loader: "style-loader" },
                    { loader: "css-loader" },
                    { loader: "less-loader" }
                ]
            },
            {
                test: /\.js$/,
                exclude: /node_modules|vue\/dist|vue-router\/|vue-loader\/|vue-hot-reload-api\//,
                loader: 'babel-loader'
            },
            {
                test: /\.(png|jpg|gif|ttf|svg|woff|eot)$/,
                loader: 'url-loader',
                query: {
                    limit: 30000,
                    name: '[name].[ext]?[hash]'
                }
            }
        ]
    },
    plugins: [
        new webpack.DefinePlugin({
            "process.env": {
                NODE_ENV: JSON.stringify("production")
            }
        })
    ]
};
```
然后执行npm run build，就会在dist目录下生成helloMsg.min.js。这即是我们这个npm包的主文件。
#### 6.修改package.json中的main字段中指向的主文件信息
package.json
```
{
	...
	"main": "dist/helloMsg.min.js",
```
#### 7.新建一个文件，名为.npmignore，是不需要发布到npm的文件和文件夹，规则和.gitignore一样。如果你的项目底下有.gitignore但是没有.npmignore，那么会使用.gitignore里面的配置。
例如我的:

.npmignore
```.*
*.md
*.yml
build/
node_modules/
src/
test/
gulpfile.js
```
至此，一个npm组件包就做完了，剩下的，只是提交到npm官网去。
## 二. 发布到npm上的流程
#### 1.前提，得有个npm账号，没有就新注册一个账号，

https://www.npmjs.com/signup?utm_source=house&utm_medium=package%20page&utm_campaign=free%20orgs&utm_term=Sign%20up%20for%20npm
#### 2.进入到项目的根目录下，运行 npm login

它会让你输入你的用户名，密码和邮箱，若登录成功，会显示：
```
Logged in as 你的名字 on https://registry.npmjs.org/.
```
#### 3.然后执行npm publish，即可将这个npm包发布到npm官网上。
#### 4.更新、发新包。

需要发新包时，需要自己手动修改package.json中的version版本号，惯例是+1啦，比如1.0.0-->1.0.1。然后npm login,npm publish。即可。