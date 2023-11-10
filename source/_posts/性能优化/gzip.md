---
title: 开启gzip
categories: 性能优化
---
   1. `npm i -D compression-webpack-plugin`

   2. 修改 *vue.config.js* 配置

   ```js
      const path = require('path')
      const CompressionPlugin = require('compression-webpack-plugin')
      module.exports = {
       ...
        configureWebpack: config => {
          if (process.env.NODE_ENV === 'production') {
            return {
              plugins: [
                new CompressionPlugin({
                  test: /\.js$|\.html$|\.css$|\.jpg$|\.jpeg$|\.png/, // 需要压缩的文件类型
                  threshold: 10240, // 归档需要进行压缩的文件大小最小值，这里是10K以上的进行压缩
                  deleteOriginalAssets: false // 是否删除原文件
                })
              ]
            }
          }
        }
      }
   ```
   3. nginx启用gzip
    ```nginx
    http {
        # 开启|关闭 gzip。
        gzip on|off;
          # 文件大于指定 size 才压缩，以 kb 为单位。
          gzip_min_length 10;
          # 压缩级别，1-9，值越大压缩比越大，但更加占用 CPU，且压缩效率越来越低。
          gzip_comp_level 2;
          # 压缩的文件类型。
          gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript;
          # 开启后如果能找到 .gz 文件，直接返回该文件，不会启用服务端压缩。
          gzip_static on|off
          # 是否添加响应头 Vary: Accept-Encoding 建议开启。
          gzip_vary on;
          # 请求压缩的缓冲区数量和大小，以 4k 为单位，32 为倍数。
          gzip_buffers 32 4K;
    }
    ```