**axios.defaults.adapter**

`adapter` 自定义适配器，允许自定义处理请求，并返回自定义内容
  // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
  adapter: function (config) {
    /* ... */
  }



**httpAdap**

```
export let httpAdap = true
if (httpAdap) {
  axios.defaults.adapter = require('./xaxios/lib/adapters/http').httpAdapter
}
```

1）若为true，则使用自定义适配器httpAdapter

*// For node use HTTP adapter*

```
const http = require('http');
const https = require('https');
const httpFollow = require('follow-redirects').http;
const httpsFollow = require('follow-redirects').https;

export function httpAdapter(config){
  let transport;
  if (config.transport) {
        transport = config.transport;
      } else if (config.maxRedirects === 0) {
        transport = isHttpsProxy ? https : http;
      } else {
        if (config.maxRedirects) {
          options.maxRedirects = config.maxRedirects;
        }
        transport = isHttpsProxy ? httpsFollow : httpFollow;
      }
  let request = transport.request(options, callback)
} 
```

**follow-redirects** 是一个 Node.js 模块，提供了 HTTP/HTTPS 请求的重定向跟随功能。该模块支持自定义重定向次数、超时时间以及 HTTPS 的验证等功能。

maxRedirects: 最大重定向次数，默认为 5
maxBodyLength: 最大响应体长度，默认为 10MB

agent: 自定义请求代理配置对象

2）若为false，则使用xhrAdapter

*// For browsers use XHR adapter*

let request = new XMLHttpRequest();



