# Webpack

官方文档：https://www.webpackjs.com/concepts/

公司基于webpack自己又封装一个打包的。搞得很多之前常识性的东西被颠覆。真心累~~ 先大概学习一下吧。大概把指南看完了。有的地方还不是特别懂原理，但原因和方案大概是了解的。对于基本的用法也有了大概的了解。至于更深层次的理解等后面吧。

这里只对指南中比较基础的用法做一下总结，达到对webpack有一个初步的认识。



webpack可以接收一些参数在 npm start 的时候传递，但从配置上看，参数这种明显是不好用的，也不够强大。所有一般会使用 `--config` 来指定对应的配置。主要关注`scripts`。

```json
//package.json
{
  "name": "webpack-demo",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "watch": "webpack --watch",
    "start": "webpack-dev-server --open --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1",
    "lodash": "^4.17.15"
  },
  "devDependencies": {
    "clean-webpack-plugin": "^3.0.0",
    "css-loader": "^3.4.2",
    "csv-loader": "^3.0.2",
    "file-loader": "^5.1.0",
    "html-webpack-plugin": "^3.2.0",
    "style-loader": "^1.1.3",
    "uglifyjs-webpack-plugin": "^2.2.0",
    "webpack": "^4.41.6",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^4.2.2",
    "xml-loader": "^1.2.1"
  }
}
```

```js
//webpack.prod.js
const webpack = require('webpack');
const merge = require('webpack-merge');
const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
const common = require('./webpack.common.js');
module.exports = merge(common, {
	devtool: 'source-map',
	plugins: [
		new UglifyJSPlugin({
			sourceMap: true
		}),
		new webpack.DefinePlugin({
			'process.env.NODE_ENV': JSON.stringify('production')
		})
	]
});
```

```js
//webpack.dev.js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
	devtool: 'inline-source-map',
	devServer: {
		contentBase: './dist',
		hot: true,
		port: 8000,
		open: true
	},
});
```

```js
//webpack.common.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const {
    CleanWebpackPlugin
} = require("clean-webpack-plugin");
const webpack = require('webpack');

module.exports = {
    // entry: './src/index.js',
    entry: {
        app: './src/index.js',
        print: './src/print.js',
        another: './src/another-module.js'
    },
    output: {
        // filename: 'bundle.js',
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, 'dist')
    },
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            title: 'Output Management'
        }),
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ],
    optimization: {
        splitChunks: {
            cacheGroups: {
                commons: {
                    name: "commons",
                    chunks: "initial",
                    minChunks: 2
                }
            }
        }
    },
    module: {
        rules: [{
            test: /\.css$/,
            use: [
                'style-loader',
                'css-loader'
            ]
        }, {
            test: /\.(png|svg|jpg|gif)$/,
            use: [
                'file-loader'
            ]
        }, {
            test: /\.(woff|woff2|eot|ttf|otf)$/,
            use: [
                'file-loader'
            ]
        }, {
            test: /\.(csv|tsv)$/,
            use: [
                'csv-loader'
            ]
        }, {
            test: /\.xml$/,
            use: [
                'xml-loader'
            ]
        }]
    }
};
```



上面的配置是看完指南后，我保留的一些感觉有用的配置。有点多，但大概结构是这样的。`module export`导出的是一个对象，而这个对象中的key是固定的，key也就是可以配置的内容。可以在官方文档中看到[所有配置](https://www.webpackjs.com/configuration/)。



其它还有很多高级特性：懒加载、热替换、代码分离、缓存、编译效率这些，后面有机会再看了。

