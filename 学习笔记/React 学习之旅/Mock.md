# Mock

<img src="https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633331208322-127cd4cb-2858-4df6-9636-b2343582d646.jpeg" alt="img" style="zoom:50%;" />

## Vue Mock

```shell
vue create mock-demo 
// 使用 axios 发送 ajax
npm install axios --save
// 使用 mockjs 产生随机数据
npm install mockjs --save-dev
// 使用 json5 解决 json 文件无法添加注释问题
npm install json5 --save-dev
```

### Vue 中使用 Mock

mock/testMockjs.js （测试用）

```js
const Mock = require('mockjs');// 导入依赖模块
var id = Mock.mock('@id'); // 得到随机的id,字符串
console.log(Mock.mock('@id'), typeof id)

var obj = Mock.mock({
  id: "@id()", // 得到随机的id,对象
  username: "@cname()", // 随机生成中文名字
  date: "@date()", // 随机生成日期
  avatar: "@image('200x200','red','#fff','avatar')", // 生成图片,参数:size, background, foreground, text
  description: "@paragraph()", // 描述
  ip: "@ip()", // IP地址
  email: "@email()" // email
})
console.log(obj)
```

mock/testJSON5.js （测试用）

```js
const fs = require('fs');
const path = require('path');
const JSON5 = require('json5');
// 读取 json 文件
function getJsonFile(filePath) {
  // 读取指定 json 文件
  var json = fs.readFileSync(path.resolve(__dirname,filePath), 'utf-8');
  // 解析并返回
  return JSON5.parse(json);
}
var json = getJsonFile('./userInfo.json5');
console.log('json', json) 
```

userInfo.json5（正式用法）

```json
{
  id: "@id()", // 得到随机的id,对象
  username: "@cname()", // 随机生成中文名字
  date: "@date()", // 随机生成日期
  avatar: "@image('200x200','red','#fff','avatar')", // 生成图片,参数:size, background, foreground, text
  description: "@paragraph()", // 描述
  ip: "@ip()", // IP地址
  email: "@email()" // email
}
```

mock/index.js （正式用法）

```js
const fs = require('fs');
const path = require('path');
const Mock = require('mockjs'); // mockjs 导入依赖模块
const JSON5 = require('json5');
// 读取 json 文件
function getJsonFile(filePath) {
  // 读取指定json文件
  var json = fs.readFileSync(path.resolve(__dirname,filePath), 'utf-8');
  // 解析并返回
  return JSON5.parse(json);
}

// 返回一个函数
module.exports = function(app){
  // 监听 http 请求
  app.get('/user/userinfo', function (rep, res) {
    // 每次响应请求时读取 mock data 的 json 文件
    // getJsonFile 方法定义了如何读取 json 文件并解析成数据对象
    var json = getJsonFile('./userInfo.json5');
    // 将 json 传入 Mock.mock 方法中，生成的数据返回给浏览器
    res.json(Mock.mock(json));
  });
}
```

vue.config.js（正式用法）

```js
module.exports = {
  devServer: {
    before: require('./mock/index.js') // 引入mock/index.js
  }
}
```

src/components/HelloWorld.vue（正式用法）

```vue
import axios from 'axios'
export default {
	name: 'HelloWorld',
	props: {
	  msg: String
  },
	mounted() {
	  axios.get('/user/userinfo')
	    .then(res => {
	      console.log(res)
	    })
	    .catch(err => {
	      console.error(err); 
	    })
	}
} 
```

### 移除 Mock

根路径新建.env.development

```txt
MOCK=true 
```

mock/index.js

```js
module.exports = function(app){
  if (process.env.MOCK == 'true') {
    // 监听http请求
    app.get('/user/userinfo', function (rep, res) {
      // 每次响应请求时读取mock data的json文件
      // getJsonFile方法定义了如何读取json文件并解析成数据对象
      var json = getJsonFile('./userInfo.json5');
      // 将json传入 Mock.mock 方法中，生成的数据返回给浏览器
      res.json(Mock.mock(json));
    });
  }
} 
```

## React  Mock

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633335351319-b21bb25e-299d-4f47-9e61-b93e53a80b2b.jpeg)

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633335351518-00385d26-2c58-4a7d-92a8-3da3a99a1104.jpeg)

Node 方式

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633335935398-4c68be4f-283c-4403-998f-657e24f8cdf9.jpeg)

```shell
node ./mockjs/server.js

打开 localhost:8090
```

![img](https://cdn.nlark.com/yuque/0/2021/jpeg/1614731/1633335935622-9e4adc32-ed2b-4ccf-9be3-d5f7a5134b81.jpeg)

后端解决跨域后 npm start









































































