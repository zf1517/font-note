express learning note
=====================

## Overview
Express 是一个保持最小规模的灵活的 Node.js Web 应用程序开发框架。
```js
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(3000, () => console.log('Example app listening on port 3000!'))
```

### Routing
主要有两种写路由的方法，第一种即简单的方法+路径+回调处理模式；第二
