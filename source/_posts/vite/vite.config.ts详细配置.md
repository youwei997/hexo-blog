---
title: vite.config.ts详细配置
date: 2023-11-11 02:58:58
tags:
categories:
---

## base打包

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

// https://vitejs.dev/config/
export default defineConfig({
  base: "./",
  // 配置相对路径，打包后的文件，会是相对路径（生产环境），以免部署到服务器。找不到文件时，会报错。
  plugins: [vue()],
});

```
## 别名设置

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { resolve } from "path";

// https://vitejs.dev/config/
export default defineConfig({
  base: "./",
  // 配置相对路径，打包后的文件，会是相对路径（生产环境），以免部署到服务器。找不到文件时，会报错。
  plugins: [vue()],
  resolve: {
    alias: {
      // __dirname 有报什么找不到。
      // 安装 npm install --dev @types/node
      testCom: resolve(__dirname, "src/components/test/"), //组件别名
      "/testImg": "/src/assets/", //静态文件别名
    },
  },
});
```

```vue
app.vue---------------
<script setup lang="ts">
import HelloWorld from "./components/HelloWorld.vue";
import testCom from "testCom/index.vue";
</script>

<template>
  <testCom />
  <HelloWorld msg="HelloWorld" />
  <img src="/testImg/logo.png" alt="" />
</template>

<style></style>
```

## 清除build项目的console.log

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { resolve } from "path";

// https://vitejs.dev/config/
export default defineConfig({
  base: "./",
  // 配置相对路径，打包后的文件，会是相对路径（生产环境），以免部署到服务器。找不到文件时，会报错。
  plugins: [vue()],
  resolve: {
    alias: {
      // __dirname 有报什么找不到。
      // 安装 npm install --dev @types/node
      testCom: resolve(__dirname, "src/components/test/"), //组件别名
      "/testImg": "/src/assets/", //静态文件别名
    },
  },
  build: {
    minify: "terser",
    // 清除build项目的console.log
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },
  },
});

```

## rollup打包 指定dist里输出的文件夹

```	js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { resolve } from "path";

// https://vitejs.dev/config/
export default defineConfig({
  base: "./",
  // 配置相对路径，打包后的文件，会是相对路径（生产环境），以免部署到服务器。找不到文件时，会报错。
  plugins: [vue()],
  resolve: {
    alias: {
      // __dirname 有报什么找不到。
      // 安装 npm install --dev @types/node
      testCom: resolve(__dirname, "src/components/test/"), //组件别名
      "/testImg": "/src/assets/", //静态文件别名
    },
  },
  build: {
    minify: "terser",
    // 清除build项目的console.log
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },
    rollupOptions: {
      //rollup打包 指定dist里输出的文件夹
      output: {
        chunkFileNames: "js/[name]-[hash].js",
        entryFileNames: "js/[name]-[hash].js",
        assetFileNames: "[ext]/[name]-[hash].[ext]",
      },
    },
  },
});

```

## element-plus 

### element-plus完整引入

```js
npm install element-plus --save
```

```js
// main.ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'

const app = createApp(App)

app.use(ElementPlus)
app.mount('#app')
```

### element-plus 按需引入

```js
npm install element-plus --save
npm install -D unplugin-vue-components unplugin-auto-import
```

```js
// vite.config.ts
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default {
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
}
```

```js
// webpack.config.js
const AutoImport = require('unplugin-auto-import/webpack')
const Components = require('unplugin-vue-components/webpack')
const { ElementPlusResolver } = require('unplugin-vue-components/resolvers')

module.exports = {
  // ...
  plugins: [
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
}
```

## vite-mock

```js
npm i vite-plugin-mock mockjs -D
//vite-plugin-mock 网站 https://github.com/vbenjs/vite-plugin-mock
```

```js
// vite.config.ts
//mockjs
import { viteMockServe } from "vite-plugin-mock";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    viteMockServe({
      mockPath: "mock",
    }),
    vue(),
  ],
});

```

## proxy开发代理

```js
// vite.config.ts
export default defineConfig({
  plugins: [vue()],
  resolve: {
    // alias: [
    //     { find: '@', replacement: resolve(__dirname, 'src') }
    // ],
    alias:{
      "@": path.resolve(__dirname, "./src"),
    }
  },
   server: {
     open: false,
     host:'localhost',
     port:8080,
     proxy:{
       "/api":{
         target:"http://localhost:3000"
       }
     }
   }
})

```

## env环境变量

```json
//package.json
{
  "name": "vite-learn",
  "private": true,
  "version": "0.0.0",
  "scripts": {
    "dev": "vite --mode dev", //--mode 指定谁，就加载哪个env文件
    "build": "vue-tsc --noEmit && vite build",
    "preview": "vite preview"
  },
}
```

```
//.env.dev
VITE_BASE_API = http://dev.com

//.env.prod
VITE_BASE_API = http://prod.com
```

```js
console.log(import.meta.env.VITE_BASE_API);
```

## gzip压缩

```js
npm i vite-plugin-compression -D
```

```js
// vite.config.ts

//gzip
import viteCompression from "vite-plugin-compression";
export default defineConfig({
  plugins: [
    viteCompression(),
    vue(),
  ],
}
```

## 图片压缩

```
npm i vite-plugin-imagemin -D
```

```js
import viteImagemin from 'vite-plugin-imagemin'

export default () => {
  return {
    plugins: [
      viteImagemin({
        gifsicle: {
          optimizationLevel: 7,
          interlaced: false,
        },
        optipng: {
          optimizationLevel: 7,
        },
        mozjpeg: {
          quality: 20,
        },
        pngquant: {
          quality: [0.8, 0.9],
          speed: 4,
        },
        svgo: {
          plugins: [
            {
              name: 'removeViewBox',
            },
            {
              name: 'removeEmptyAttrs',
              active: false,
            },
          ],
        },
      }),
    ],
  }
}
```

