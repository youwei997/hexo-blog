---
title: webpack.config.js基本使用
date: 2023-11-11 13:59:11
tags: webpack
categories: 前端
---

```js
const HtmlWebpackPlugin = require("html-webpack-plugin");
// const { CleanWebpackPlugin } = require("clean-webpack-plugin");
module.exports = {
  mode: process.env.NODE_ENV === "development" ? "development" : "production",
  // 入口文件
  entry: "./src/index.ts",
  // 输出到某个文件
  output: {
    filename: "main.js",
    clean: true, // 输出之前清理文件，开启这个就可以不用 clean-webpack-plugin
  },
  resolve: {
    // 自动解析文件拓展。引入js文件时，无需再加上文件后缀
    extensions: [".js"],
  },
  module: {
    // 匹配文件名，使用loader
    rules: [
      {
        test: /\.tsx?$/,
        use: "ts-loader",
        // 排除某些文件夹下对应的文件
        exclude: /node_module/,
      },
    ],
  },
  // 开发环境启用source-map，用于定位文件代码
  devtool: process.env.NODE_ENV === "development" ? "inline-source-map" : false,
  devServer: {
    // 基于哪个目录运行代码
    // contentBase: "./dist",
    // 启用压缩
    compress: false,
    // host
    host: "localhost",
    //端口
    port: 8089,
    // 监测html文件
    watchFiles: ["./src/index.html"],
  },
  plugins: [
    // new CleanWebpackPlugin({ cleanOnceBeforeBuildPatterns: ["./dist"] }),
    new HtmlWebpackPlugin({ template: "./src/index.html" }),
  ],

  // 控制台显示哪些信息
  stats: "errors-only",
};
```

## package.json

```json
{
  "name": "typescript-lison",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "cross-env NODE_ENV=development webpack-dev-server --config ./build/webpack.config.js",
    "build": "cross-env NODE_ENV=production webpack --config ./build/webpack.config.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "cross-env": "^7.0.3",
    "eslint": "^8.25.0",
    "ts-loader": "^9.4.1",
    "typescript": "^4.8.4",
    "webpack": "^5.74.0",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.11.1"
  },
  "dependencies": {
    "clean-webpack-plugin": "^4.0.0",
    "html-webpack-plugin": "^5.5.0"
  }
}
```
