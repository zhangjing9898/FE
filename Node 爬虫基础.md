
首先，引入这些包
```
let rp = require("request-promise");
let cheerio = require("cheerio");
var MongoClient = require('mongodb').MongoClient;
let htmlDecode = require('./htmlDecode');
let fs = require("fs");   
let iconv=require('iconv-lite') 
let http=require("http");
```

htmlDecode 用于字符转换

cheerio是nodejs的抓取页面模块，为服务器特别定制的，快速、灵活、实施的jQuery核心实现。适合各种Web爬虫程序。

```
async function getCourseInfo(db) {
    for (let offset = 2; offset < 6; offset ++) {
        let res = await rp({url: `https://tuchong.com/rest/sites/1615439,1615432,1615443,1615461,1615437/posts?count=10&page=${offset}&before_timestamp=1516669276`});
        let state = JSON.parse(htmlDecode(res))
        console.log(state);
        db.collection("course").insert(state);
    }
}
```

这里的async和await有一篇文章写的很好
https://segmentfault.com/a/1190000011813934
