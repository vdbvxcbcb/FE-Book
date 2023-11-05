

src/client1.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
    .message {
      width: 600px;
      min-height: 400px;
      padding: 15px;
      border: 1px solid #ccc; 
    }
    .message .a {
      text-align: right;
    }
    .message li {
      line-height: 30px;
      list-style: none;
    }
  </style>
</head>

<body>
  <h4>当前用户：小叶</h4>
  <div class="wrap">
    <input type="text" class="input">
    <button class="btn-send">发送给小秋</button>
  </div>
  <ul class="message"></ul>

  <script>
    window.onload = () => {
      let elInput = document.querySelector('.input');
      let elUl = document.querySelector('.message');
      let ws = null;
      let msgData = {
        fromUserId: 1,
        toUserId: 2,
        type: 'open',
        content: '',
      };
      
      function connectWebsocket() {
        ws = new WebSocket('ws://localhost:9000');

        // 连接成功
        ws.onopen = () => {
          console.log('连接服务端WebSocket成功');
          ws.send(JSON.stringify(msgData));
        };

        // 监听服务端消息
        ws.onmessage = (msg) => {
          let message = JSON.parse(msg.data);
          console.log('收到的消息：', message)
          elUl.innerHTML += `<li class="b">小秋：${message.content}</li>`;
        };

        // 连接失败
        ws.onerror = () => {
          console.log('连接失败，正在重连...');
          connectWebsocket();
        };

        // 连接关闭
        ws.onclose = () => {
          console.log('连接关闭');
        };
      };
      connectWebsocket();

      document.querySelector('.btn-send').addEventListener('click', () => {
        let msg = { ...msgData, content: elInput.value, type: 'text' };
        // 发送消息
        ws.send(JSON.stringify(msg));
        elUl.innerHTML += `<li class="a">${elInput.value}：小叶</li>`;  
        elInput.value = '';  
      });

    };

  </script>
</body>

</html>
```

src/client2.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
      .message {
        width: 600px;
        min-height: 400px;
        padding: 15px;
        border: 1px solid #ccc;    
      }
      .message .a {
        text-align: right;
      }
      .message li {
        line-height: 30px;
        list-style: none;
      }
  </style>
</head>

<body>
  <h4>当前用户：小秋</h4>
  <div class="wrap">
    <input type="text" class="input">
    <button class="btn-send">发送给小叶</button>
  </div>
  <ul class="message"></ul>

  <script>
    window.onload = () => {
      let elInput = document.querySelector('.input');
      let elUl = document.querySelector('.message');
      let ws = null;
      let msgData = {
        fromUserId: 2,
        toUserId: 1,
        type: 'open',
        content: '',
      };

      function connectWebsocket() {
        ws = new WebSocket('ws://localhost:9000');

        // 连接成功
        ws.onopen = () => {
          console.log('连接服务端WebSocket成功');
          ws.send(JSON.stringify(msgData));
        };

        // 监听服务端消息
        ws.onmessage = (msg) => {
          let message = JSON.parse(msg.data);
          console.log('收到的消息：', message)
          elUl.innerHTML += `<li class="b">小叶：${message.content}</li>`;
        };

        // 连接失败
        ws.onerror = () => {
          console.log('连接失败，正在重连...');
          connectWebsocket();
        };

        // 连接关闭
        ws.onclose = () => {
          console.log('连接关闭');
        };
      };
      connectWebsocket();

      document.querySelector('.btn-send').addEventListener('click', () => {
        let msg = { ...msgData, content: elInput.value, type: 'text' };
        ws.send(JSON.stringify(msg));    
        elUl.innerHTML += `<li class="a">${elInput.value}：小秋</li>`;
        elInput.value = '';
      });
        
    }

  </script>
</body>

</html>
```

server/app.js

```js
const path = require('path');
const express = require('express');
const app = express();
const server = require('http').Server(app);
const WebSocket = require('ws');

const wss = new WebSocket.Server({ server: server });

wss.on('connection', (ws) => {
  
  // 监听客户端消息
  ws.on('message', (message) => {
    console.log(wss.clients.size);
    let msgData = JSON.parse(message);   
    if (msgData.type === 'open') {
      // 初始连接时标识会话
      ws.sessionId = `${msgData.fromUserId}-${msgData.toUserId}`;
    } else {
      let sessionId = `${msgData.toUserId}-${msgData.fromUserId}`;
      wss.clients.forEach(client => {
        if (client.sessionId === sessionId) {
          client.send(message);
        }
      })  
    }
  })

  // 连接关闭
  ws.on('close', () => {
    console.log('连接关闭');  
  });
});

app.get('/', function (req, res) {
  res.sendFile(path.join(__dirname, '../src/client1.html'));
})
app.get('/2', function (req, res) {
  res.sendFile(path.join(__dirname, '../src/client2.html'));
})

server.listen(9000, function () {
  console.log('http://localhost:9000');
});
```







