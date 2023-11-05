## require 

<img src="https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1683110095007-d92810bb-e9e0-411d-98fb-2c0f0c648377.jpeg" alt="bedab714eb44481ea7a147390fc5fcd6" style="zoom: 50%;" />

- `require`支持缓存
- `require`只能按 UTF-8 读取内容
- `require`是同步的
- `require`读取 JSON 文件可以省略`.json`后缀

### 引入的模块会被 Node 缓存

示例

test-module.js

```js
console.log('1');

module.exports = {
    name:'0'
}
```

```js
const m1 = require('./test-module')
m1.name = '2'
const m2 = require('./test-module')
console.log(m2.name);
```

运行后的输出结果输出如下

```sh
1
2
```

从这就可以得出结论Node会缓存引入的模块，name如何查看已经被缓存的模块

### 查看缓存

通过 [require.cache](http://nodejs.cn/api/modules/require_cache.html) 即可获取被缓存的模块：

- 返回一个对象

```js
const m1 = require('./test-module')
console.log(require.cache);
```

### 删除缓存

既然`require.cache`返回内容是一个普普通通的对象，name可以通过`delete`关键字进行移除

```js
const m1 = require('./test-module')
m1.name = '2'
console.log(m1.name);

delete require.cache[`${__dirname}/test-module.js`]
const m2 = require('./test-module')
console.log(m2.name);
```

内容输出如下

```sh
1
2
1
0
```

## 全局对象

Node.js 全局对象是指在 Node.js 运行环境中自动创建的对象，可以在任何地方访问，而不需要显式地导入或引入。

在 node 中，我们通过 var 定义一个变量，它只是在当前模块中有一个变量，不会放到全局中：

![Snipaste_2023-04-25_13-10-53](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682399466887-ce022105-487b-498b-a637-4becf5e38073.png)

下面是 Node.js 中一些常见的全局对象：

1. `global` 对象：它是 Node.js 的顶级对象，类似于浏览器中的 `window` 对象。它包含了 Node.js 中所有的全局变量和函数，可以被任何模块访问。

2. `process` 对象：它提供了有关当前 Node.js 进程的信息，例如进程 ID、命令行参数、环境变量等。它还可以用于控制进程行为，例如终止进程、设置进程标题等。

3. `console` 对象：它提供了在控制台中输出信息的方法，例如 `console.log()`、`console.error()` 等。

4. `Buffer` 对象：它用于处理二进制数据，例如读写文件、网络传输等。

5. `setTimeout` 和 `setInterval` 函数：它们用于在指定的时间间隔后执行函数或代码。

6. `require` 函数：它用于导入模块，使得在当前模块中可以使用其他模块中的变量和函数。

7. `module` 和 `exports` 对象：它们用于定义模块和对外提供接口。

1、 获取命令行参数

某些情况下执行 node 程序的过程中，我们可能希望给 node 传递一些参数：

如果我们这样来使用程序，就意味着我们需要在程序中获取到传递的参数：

```sh
node index.js env=development coderwhy
```

 获取参数其实是在 process 的内置对象中的；

![Snipaste_2023-04-25_13-03-54](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682399074640-8a562db0-6420-4e6e-8fba-3ae02c62385f.png)

argv：argument vector的缩写，传入的具体参数。

- vector 翻译过来是矢量的意思，在程序中表示的是一种数据结构。

- 在 C++、Java 中都有这种数据结构，是一种数组结构；

- 在 JavaScript 中也是一个数组，里面存储一些参数信息；

process.argv[0] 是 Node.js 可执行文件的路径，

process.argv[1] 是当前脚本文件的路径，

实际的命令行参数从 process.argv[2] 开始。

如果在命令行中执行以下命令：

```sh
node index.js hello world
```

那么，在 index.js 中可以通过 process.argv[2] 和 process.argv[3] 来获取命令行参数中的值，即 "hello" 和 "world"

2、处理命令行选项

命令行选项通常以单个横线 "-" 或双横线 "--" 开头，例如 --verbose 或 -v。

可以使用 process.argv.slice(2) 方法来获取除了 Node.js 可执行文件路径和当前脚本文件路径之外的所有命令行参数，

然后使用数组方法来处理这些参数。

例如，如果在命令行中执行以下命令：

```sh
node index.js --verbose --port 3000
```

那么，在 index.js 中可以使用以下代码来处理命令行选项：

```js
const args = process.argv.slice(2);

const options = {};
for (let i = 0; i < args.length; i++) {
  const arg = args[i];
  if (arg.startsWith('--')) {
    const key = arg.slice(2);
    const val = args[i + 1];
    options[key] = val;
    i++;
  } else if (arg.startsWith('-')) {
    const key = arg.slice(1);
    const val = args[i + 1];
    options[key] = val;
    i++;
  }
}

console.log(options);
// Output: { verbose: undefined, port: '3000' }
```
### process

1. 进程信息获取：

- `process.pid`：获取当前进程 ID。
- `process.platform`：获取运行进程的操作系统类型（aix、drawin、freebsd、linux、openbsd、sunos、win32）。
- `process.version`：获取 Node.js 版本号。
- `process.argv`：获取命令行参数。
- `process.execPath` ：获取执行当前脚本的 Node 二进制文件的绝对路径。
- `process.cwd()`：获取当前工作目录。
- `process.env`：获取当前 Shell 的所有环境变量。
- `process.execArgv`：返回一个数组，成员是命令行下执行脚本时，在 Node 可执行文件与脚本文件之间的命令行参数
```
  console.log(process.execArgv);
```


```
  node --inspect process.js
  
  [ '--inspect' ]
```

2. 进程控制方法：

- `process.exit([code])`：终止进程，可选的退出码为整数类型，表示进程的退出状态。
- `process.kill(pid[, signal])`：发送信号给进程，终止进程。pid 为进程 ID，signal 为信号名称或数字。
- `process.title = 'newTitle'`：设置进程标题。
- `process.memoryUsage()`：返回 Node.js 进程的内存使用情况。
- `process.chdir()`：切换工作目录到指定目录

3. 事件处理：

- `process.on('exit', (code) => {})`：

  当 Node.js 进程因以下原因之一即将退出时，则会触发 `exit` 进程退出事件，code 表示进程的退出状态。

  当 Node.js 清空其事件循环并且没有其他工作要安排时，会触发 `beforeExit` 事件。 

  如果是显式调用 process.exit()退出，或者未捕获的异常导致退出，那么 `beforeExit` 不会触发。

  通常 Node.js 进程将在没有调度工作时退出，但是在 `beforeExit` 事件上注册的监听器可以进行异步调用使 Node.js 进程继续。

  ```js
  process.on('beforeExit', (code) => {
    console.log('进程 beforeExit 事件的代码: ', code);
  });
  
  process.on('exit', (code) => {
    console.log('进程 exit 事件的代码: ', code);
  });
  
  console.log('此消息最新显示');
  
  // 打印:
  // 此消息最新显示
  // 进程 beforeExit 事件的代码: 0
  // 进程 exit 事件的代码: 0
  ```

- `process.on('uncaughtException', (err) => {})`：

  捕获未处理的异常事件，err 表示未处理的异常对象。

- `process.on('SIGINT', () => {})`：

  接收到信号 SIGINT 时触发的事件，通常用于处理 Ctrl+C 终止进程的情况。

## 特殊的全局对象

` __dirname`：获取当前文件所在的路径

注意：不包括后面的文件名

` __filename`：获取当前文件所在的路径和文件名称：

注意：包括后面的文件名

![Snipaste_2023-04-25_12-23-01](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682396596703-3767250c-ecbb-4aec-845c-c5d3d76b8270.png)

## 内置模块 path

Mac OS、Linux 和 window上的路径时不一样的

window上会使用 \或者 \\ 来作为文件路径的分隔符，当然目前也支持 /；

在Mac OS、Linux的 Unix 操作系统上使用 / 来作为文件路径的分隔符；

如果我们在 window 上使用 \ 来作为分隔符开发了一个应用程序，要部署到 Linux 上面应该怎么办呢？

显示路径会出现一些问题，所以为了屏蔽差异，在开发中对于路径的操作我们可以使用 path 模块；

### 引入 path 模块

```js
const path = require('path');
```

### 从路径中获取信息

- dirname：获取文件的父文件夹；
- basename：获取文件名；
- extname：获取文件扩展名；

```js
const myPath = '/Users/coderwhy/Desktop/Node/课堂/PPT/01_邂逅Node.pdf';

const dirname = path.dirname(myPath);
const basename = path.basename(myPath);
const extname = path.extname(myPath);

console.log(dirname); // /Users/coderwhy/Desktop/Node/课堂/PPT
console.log(basename); // 01_邂逅Node.pdf
console.log(extname); // .pdf
```

### 路径的拼接

使用 path.join() 方法可以避免手动拼接路径时出现错误，例如路径分隔符等问题。

在处理路径时，应该始终使用绝对路径，避免相对路径带来的问题。

- 如果我们希望将多个路径进行拼接，但是不同的操作系统可能使用的是不同的分隔符；
- 这个时候我们可以使用`path.join`函数；

```js
path.join('/user', 'why', 'abc.txt');
path.join('/a', '/b', '/c')     // /a/b/c
path.join('a', 'b', 'c')        //  a/b/c
path.join('/a', 'b','..', 'c')  // /a/c    ".."会向前跳一个目录
```

path.join() 参数必须是字符串类型，否则会抛出 TypeError 异常。

### 将文件和某个文件夹拼接

path.resolve()  将多个路径解析为一个绝对路径

- `resolve`函数会判断我们拼接的路径前面是否有 `/`或`../`或`./`；
- 如果有表示是一个绝对路径，会返回对应的拼接路径；
- 如果没有，那么会和当前执行文件所在的文件夹进行路径的拼接

```js
path.resolve('abc.txt'); // /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/06_常见的内置模块/02_文件路径/abc.txt
path.resolve('/abc.txt'); // /abc.txt
path.resolve('/User/why', 'abc.txt'); // /User/why/abc.txt
path.resolve('User/why', 'abc.txt'); // /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/06_常见的内置模块/02_文件路径/User/why/abc.txt
```

resolve 其实我们在 webpack 中也会使用：

```js
const CracoLessPlugin = require('craco-less');
const path = require("path");

const resolve = dir => path.resolve(__dirname, dir);

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    }
  ],
  webpack: {
    alias: {
      "@": resolve("src"),
      "components": resolve("src/components")
    }
  }
}
```

```js
// 获取node.exe的绝对路径
console.log(process.execPath);//D:\nodejs\node.exe

// 存放当前文件（即server.js）文件夹的绝对路径
console.log(__dirname);//D:\nodeTest\node_path\lib

// 当前文件（即server.js）的绝对路径
console.log(__filename);//D:\nodeTest\node_path\lib\server.js

// 从所传入的文件路径（相对或绝对）中获取存放传入文件的文件夹的相对或绝对路径 
//（例如 传入 public/home.html 则返回的是public）
console.log(path.dirname(__filename));//D:\nodeTest\node_path\lib

// 执行当前脚本（即server.js）的位置 （例如 在根目录下执行 node ./xxx/xxx/a.js 则返回的是根目录地址 ）
console.log(process.cwd());//D:\nodeTest\node_path\lib

// 'a/b/c'和'../src' 组合而成的绝对路径 文件或文件夹都行
// 例如 console.log(path.resolve('a/b/c', '../src'));//D:\nodeTest\node_path\lib\a\b\src
console.log(path.resolve(__dirname, '../public'));//D:\nodeTest\node_path\public

//'a/b/c'和'../src'组成的相对路径
//console.log(path.join('a/b/c', '../src'));//a\b\src
console.log(path.join(__dirname, '../public'));//D:\nodeTest\node_path\public

// 相当于path.resolve(__dirname, '../public/home.html')或path.join(__dirname, '../public/home.html')
// 但传入的必须是文件路径，而不是文件夹路径，而且当文件不存在时会抛出异常
console.log(require.resolve('../public/home.html'));//D:\nodeTest\node_path\public\home.html
```

## 内置模块 fs

Node.js 中的文件操作是异步的，即文件操作完成之前，代码会继续执行。

因此，在进行文件操作时需要使用回调函数或 Promise 等方式来处理文件操作完成后的结果。

在进行文件操作时，可能会出现各种错误，例如文件不存在、权限不足等。

因此，在进行文件操作时，需要对可能出现的错误进行处理，例如使用 try-catch 块来捕获异常，或者在回调函数中判断 err 参数是否为 null。

文件系统的 API 非常多，这些 API 大多数都提供三种操作方式：

- 方式一：同步操作文件：代码会被阻塞，不会继续执行；
- 方式二：异步回调函数操作文件：代码不会被阻塞，需要传入回调函数，当获取到结果时，回调函数被执行；
- 方式三：异步Promise操作文件：代码不会被阻塞，通过 `fs.promises` 调用方法操作，会返回一个Promise，可以通过then、catch进行处理；

方式一：同步操作文件

```js
// 1.方式一: 同步读取文件
const state = fs.statSync('../foo.txt');
console.log(state);

console.log('后续代码执行');
```

方式二：异步回调函数操作文件

Node.js 默认的异步操作是 callback 风格
1. err: 如果程序处理出现异常，错误信息放在回调函数的第一个参数，如果没有错误 err 为 null
2. returnValue：程序正常处理完成后结果放在回调函数第二个参数

```js
// 2.方式二: 异步读取
fs.stat("../foo.txt", (err, state) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log(state);
})
console.log("后续代码执行");
```

promisify

Node.js 使用回调风格一方面是因为性能原因，一方面是因为 Node.js 诞生的时候 Promise 规范还没有制定，

在 Promise 规范制定后 Node.js 通过内置模块 util 提供的 `promisify` 方法可以把所有标准 callback 风格方法转成 promise 风格方法

```js
const util = require('util');
const fs = require('fs');

const stat = util.promisify(fs.stat);
stat('.').then(stats => {
  // 使用 stats
}).catch(error => {
  // 处理错误
});
```

方式三：异步Promise操作文件

```js
// 3.方式三: Promise方式
fs.promises.stat("../foo.txt").then(state => {
  console.log(state);
}).catch(err => {
  console.log(err);
})
console.log("后续代码执行");
```

### 引入 fs 模块

```js
const fs = require('fs');
```

### 获取文件信息

 fs.stat 方法获取文件基本信息，如果打印 stats 对象可以得到文件基本属性

```js
Stats {
  dev: 16777220,
  mode: 16877,
  nlink: 3,
  uid: 501,
  gid: 20,
  rdev: 0,
  blksize: 4096,
  ino: 4301278483,
  size: 96,
  blocks: 0,
  atimeMs: 1588483554315.173,
  mtimeMs: 1588483370684.5703,
  ctimeMs: 1588483370684.5703,
  birthtimeMs: 1588483342193.8625, 
  atime: 2020-05-03T05:25:54.315Z, // 访问时间
  mtime: 2020-05-03T05:22:50.685Z, // 文件内容修改时间
  ctime: 2020-05-03T05:22:50.685Z, // 文件状态修改时间
  birthtime: 2020-05-03T05:22:22.194Z // 创建时间
}
```

stats 对象还提供了几个非常有用的属性、方法获取文件的更多信息，比较常用的有

1. stats.isDirectory()：判断文件是否是文件夹
2. stats.isFile()：判断文件是否是普通文件
3. stats.isSymbolicLink()：判断文件是否是软链接
4. stats.size：获取文件字节数

```js
const fs = require('fs');

fs.stat('.', (err, stats) => {
  if(!err) {
    console.log(stats);
    // 是否是文件类型
    console.log(stats.isFile()) // false
    // 是否是文件夹类型
    console.log(stats.isDirectory()) // true
    console.log(stats.size);
  }
});
```

### 文件（夹）是否存在

fs.existsSync 判断路径是否存在

fs.existsSync(path) 用来判断路径是否存在，如果存在则返回 `true`，否则返回 `false`

```javascript
if (fs.existsSync('/etc/passwd')) {
  console.log('该路径已存在');
}
```

方法存在异步版本 `fs.exists(path, callback)` 但已经被废弃。 

fs.access 测试用户文件(夹)

```javascript
fs.access('./fileForRead.txt', function(err){
  if(err) throw err;
  console.log('fileForRead.txt存在');
});

fs.access('./fileForRead2.txt', function(err){
  if(err) throw err;
  console.log('fileForRead2.txt存在');
});
```

`fs.access()`除了判断文件是否存在（默认模式），还可以用来判断文件的权限。

| mode 常量 | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| `F_OK`    | 方法默认值，表明文件对调用进程可见，可用于判断文件是否存在   |
| `R_OK`    | 表明调用进程可以读取文件                                     |
| `W_OK`    | 表明调用进程可以写入文件                                     |
| `X_OK`    | 表明调用进程可以执行文件，在 Windows 上无效（表现等同 `fs.constants.F_OK`） |

```js
const file = './package.json';

// 检查当前目录中是否存在该文件
fs.access(file, fs.constants.F_OK, (err) => {
  console.log(`${file} ${err ? '不存在' : '存在'}`);
});

// 检查文件是否可读
fs.access(file, fs.constants.R_OK, (err) => {
  console.log(`${file} ${err ? '不可读' : '可读'}`);
});

// 检查文件是否可写
fs.access(file, fs.constants.W_OK, (err) => {
  console.log(`${file} ${err ? '不可写' : '可写'}`);
});

// 检查当前目录中是否存在该文件，以及该文件是否可写
fs.access(file, fs.constants.F_OK | fs.constants.W_OK, (err) => {
  if (err) {
    console.error(
      `${file} ${err.code === 'ENOENT' ? '不存在' : '只可读'}`);
  } else {
    console.log(`${file} 存在，且它是可写的`);
  }
});
```

在调用 `fs.open()`、`fs.readFile()` 或 `fs.writeFile()` 之前，不要使用 `fs.access()` 检查文件的可访问性。 

这样做会引入竞争条件，因为其他进程可能会在两次调用之间更改文件的状态。 

不推荐使用方式

```js
fs.exists('myfile', (exists) => {
  if (exists) {
    fs.open('myfile', 'r', (err, fd) => {
      if (err) throw err;
      readMyData(fd);
    });
  } else {
    console.error('myfile 不存在');
  }
});
```

推荐使用方式

```js
fs.open('myfile', 'r', (err, fd) => {
  if (err) {
    if (err.code === 'ENOENT') {
      console.error('myfile 不存在');
      return;
    }

    throw err;
  }

  readMyData(fd);
});
```

### 文件操作

#### 读写文件

文件的读写：

- `fs.readFile(path[, options], callback)`：读取文件的全部内容；
- `fs.writeFile(file, data[, options], callback)`：在文件中写入内容，写入文件时，如果文件不存在，则会创建并写入，如果文件存在，会覆盖文件内容

文件写入：

```js
fs.writeFile('../foo.txt', content, {}, err => {
  if (err) throw err;  
  console.log(err);
})
```

在上面的代码中，你会发现有一个大括号没有填写任何的内容，这个是写入时填写的option参数：

- flag：写入的方式。
- encoding：字符的编码；

flag的值有很多：

- `w` 打开文件写入，默认值；
- `w+`打开文件进行读写，如果不存在则创建文件；
- `r`打开文件读取，读取时的默认值；
- `r+` 打开文件进行读写，如果不存在那么抛出异常；
- `a`打开要写入的文件，将流放在文件末尾。如果不存在则创建文件；
- `a+`打开文件以进行读写，将流放在文件末尾。如果不存在则创建文件

编码：

- https://www.jianshu.com/p/899e749be47c
- 目前基本用的都是 utf-8 编码；

文件读取：

- 如果不填写encoding，返回的结果是Buffer；

```js
fs.readFile('../foo.txt', {encoding: 'utf-8'}, (err, data) => {
  if (err) throw err;
  console.log(data);
})
```

#### 删除文件

```js
fs.unlink('path/file.txt', (err) => {
  if (err) throw err;
  console.log('path/file.txt was deleted');
}); 
```

fs.unlink() 方法删除文件 "path/file.txt"，并在删除完成后执行回调函数。

#### 复制文件

1、将一个文件复制到另一个文件中：

同步地复制 src 到 dest。 

默认情况下，如果 dest 已经存在则会覆盖，可以通过 mode 参数（[文件 copy 常量](http://nodejs.cn/api/fs.html#fs_file_copy_constants)）修改其行为。

```js
// 默认情况下将创建或覆盖 destination.txt。
fs.copyFileSync('source.txt', 'destination.txt');
console.log('source.txt was copied to destination.txt');

// 通过使用 COPYFILE_EXCL，如果 destination.txt 存在，则该操作将失败。
fs.copyFileSync('source.txt', 'destination.txt', fs.constants.COPYFILE_EXCL);
```

将文件 "/path/to/source/file" 的内容复制到文件 "/path/to/destination/file" 中。

2、将一个文件的内容复制到另一个文件中：

将 readStream 的内容写入到 writeStream 中

```js
const readStream = fs.createReadStream('/path/to/source/file');
const writeStream = fs.createWriteStream('/path/to/destination/file');

readStream.pipe(writeStream);
```

#### 文件内容追加

##### writeFile

```JS
// 写入文件内容（如果文件不存在会创建一个文件）
// 写入时会先清空文件
fs.writeFile(filePath, '写入成功：', function (err) {
  if (err) {
    throw err;
  }
  // 写入成功后读取测试
  const data = fs.readFileSync(filePath, 'utf-8');
  console.log('new data -->'+data);
});

// 通过文件写入并且利用 flag 也可以实现文件追加
fs.writeFile(filePath, '追加的数据', {'flag':'a'}, function (err) {
  if (err) {
	throw err;
  }
  console.log('success');
  const data=fs.readFileSync(filePath, 'utf-8')
  // 写入成功后读取测试
  console.log('追加后的数据 -->'+data);
});
```

##### appendFile

```js
fs.appendFile(filename, data, [options], callback)
```

1. 第一个必选参数 filename ，表示读取的文件名
2. 第二个参数 data，data 可以是任意字符串或者缓存
3. 第三个参数 option 是一个对象，与write的区别就是[options]的flag默认值是”a”，所以它以追加方式写入数据.

以异步的方式将 data 插入到文件里，如果文件不存在会自动创建

```js
// -- 异步另一种文件追加操作(非覆盖方式)
// 写入文件内容（如果文件不存在会创建一个文件）
fs.appendFile(filePath, '新数据', function (err) {
  if (err) {
    throw err;
  }
  // 写入成功后读取测试
  const data=fs.readFileSync(filePath, 'utf-8');
  console.log(data);
});

// -- 同步另一种文件追加操作(非覆盖方式)
fs.appendFileSync(filePath, '同步追加一条新数据');
```

#### 修改文件模式

```js
fs.chmod(path, mode, callback)
```

mode 使用八进制表示 `0o765` (0o 表示数字是八进制)，和 linux 文件权限规则一致，后三位数字分别代表

1. 文件 owner 权限

2. 所在 group 权限

3. 其它 group 权限

```js
fs.chmod('my_file.txt', 0o775, (err) => {
  if (err) throw err;
  console.log('文件 my_file.txt 的权限已被更改');
});
```

#### 文件描述符

文件描述符（File descriptors）是什么呢？

在 POSIX 系统上，对于每个进程，内核都维护着一张当前打开着的文件和资源的表格。

- 每个打开的文件都分配了一个称为文件描述符的简单的数字标识符。
- 在系统层，所有文件系统操作都使用这些文件描述符来标识和跟踪每个特定的文件。
- Windows 系统使用了一个虽然不同但概念上类似的机制来跟踪资源。
- 为了简化用户的工作，Node.js 抽象出操作系统之间的特定差异，并为所有打开的文件分配一个数字型的文件描述符。

文件描述是在操作系统中管理的在进程中打开文件所关联的一些数字或者索引。

操作系统通过指派一个唯一的整数给每个打开的文件用来查看关于这个文件

| Stream | 文件描述 | 描述     |
| ------ | -------- | -------- |
| stdin  | 0        | 标准输入 |
| stdout | 1        | 标准输出 |
| stderr | 2        | 标准错误 |

`console.log('Log')` 是 `process.stdout.write('log')` 的语法糖。

一个文件描述是 open 以及 openSync 方法调用返回的一个数字

```js
const fd = fs.openSync('myfile', 'a');
console.log(typeof fd === 'number'); // true
```

在 Node.js 中，每操作一个文件，文件描述符是递增的，文件描述符一般从 3 开始，

因为前面有 0、1、2 三个比较特殊的描述符，

分别代表 `process.stdin`（标准输入）、`process.stdout`（标准输出）和 `process.stderr`（错误输出）。

`fs.open()` 方法用于分配新的文件描述符。 

一旦被分配，则文件描述符可用于从文件读取数据、向文件写入数据、或请求关于文件的信息。

```js
// 获取文件描述符
fs.open("../foo.txt", 'r', (err, fd) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log(fd);
  // 通过描述符去获取文件的信息
  fs.fstat(fd, (err, state) => {
    console.log(state);
  })
})
```

#### 指定位置读写文件操作

fs.readFile 使用相当简单，在大部分读取小文件的时候我们都应该使用这个方法，但 fs.readFile() 会把文件全部内容读取，

如果想精确读取部分文件内容，Node.js 也提供了类似 C 语言 fopen、fgetc、fclose 的操作

在 Node.js 中读取一个文件同样有三步

1. fs.open()：分配（打开）文件描述符
2. fs.read()：读取文件内容
3. fs.close()：关闭文件描述符

##### fs.open(path[, flags[, mode]], callback)

通过 fs.open 方法可以打开一个文件，获取分配到的文件描述符，方法参数含义：

- **path** 文件路径（实际还能是 Buffer、URL）
- **flags** [文件标志位](http://nodejs.cn/api/fs.html#fs_file_system_flags)，默认值 r（read 缩写），标识文件用于读取
- **mode** 文件模式，默认值 0o666 （rw-）可读写
- **callback**

- - err
  - fd 分配到的文件描述符 （file description）

##### fs.read(fd, [options,] callback)

fs.read 用于从文件描述符中读取数据，方法参数含义：

- **fd** 文件描述符
- **options** 可选项，不设置使用下述默认值

- - buffer：数据（从 fd 读取）将被写入的缓冲区，默认会申请一个新的缓冲区 `Buffer.alloc(16384)`
  - offset：buffer 开始写入的偏移量，默认值为 0
  - length：需要读取的字节数，默认使用 `buffer.length`
  - position：从文件中开始读取数据的位置；如果 position 为 null，则从当前文件位置读取，并重置文件位置到上次位置；如果 position 是整数，则文件位置会被保持。默认值为 null

- **callback**

- - err
  - bytesRead
  - buffer

fs.read 还有一个需要把参数写全的重载 fs.read(fd, buffer, offset, length, position, callback)

##### fs.close(fs, callback)

fs.close 用于关闭文件描述符，大多数操作系统都会限制同时打开的文件描述符数量，

因此当操作完成时关闭描述符非常重要。 如果不这样做将导致内存泄漏，最终导致应用程序崩溃

示例

```txt
0123456789
abcdefghigklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

```js
const fs = require('fs');
const promisify = require('util').promisify;

const open = promisify(fs.open);
const read = promisify(fs.read);
const close = promisify(fs.close);

async function test() {
  const fd = await open('./test.txt');
  const readOptions = {
    // buffer: Buffer.alloc(26), 异步调用默认可以不设置，如果希望读取的字节写入指定的缓冲区可以指定
    position: 11, // 从第 11 个字节开始读取，读取后文件位置被重置
    length: 26, // 读取 26 个字节
  };

  const { bytesRead: bytesRead1, buffer: buf1  } = await read(fd, readOptions);

  console.log(`第一次读取字节数: ${bytesRead1}`);
  console.log(`第一次读取数据内容: ${buf1.toString()}`);

  // 不指定 position，文件位置每次读取后会保持
  const { bytesRead: bytesRead2, buffer: buf2 } = await read(fd, { length: 1 });
  console.log(`第二次从文件重置后位置读取 ${bytesRead2} 字节内容: ${buf2.toString()}`);

  const { bytesRead: bytesRead3, buffer: buf3 } = await read(fd, { length: 1 });
  console.log(`第三次从文件当前位置读取 ${bytesRead3} 字节内容: ${buf3.toString()}`);

  await close(fd);
  console.log(`文件描述符 ${fd} 已关闭`);
}

test();
```

```txt
第一次读取字节数: 26
第一次读取数据内容: abcdefghigklmnopqrstuvwxyz
第二次从文件重置后位置读取 1 字节内容: 0
第三次从文件当前位置读取 1 字节内容: 1
文件描述符 20 已关闭
```

- 程序第一次从第 11 个字节开始读取 `test.txt` 内容，一共读取 26 个字节
- 第一次读取设置了 position 属性，读取完成后文件指针位置被重置为 0
- 第二次读取没有设置 position 读取一个字节后，文件位置停留在 1
- 第三次读取直接从文件位置 1 开始读取

除非希望精确控制，否则不要使用这种方式读取文件，手工控制缓冲区、文件位置指针很容易出现各种意外状况

### 文件夹操作

#### fs.Dir & fs.Dirent

fs.Dir 是可迭代的目录流的类，fs.Dirent 是遍历 fs.Dir 获得的目录项，可以是文件或目录中的子目录

#### fs.Dir

- dir.path：目录的只读路径
- dir.read()：不传入 callabck 函数则返回 Promise，读取迭代器下一个目录项，返回一个 Promise，resolve 后得到 fs.Dirent 或 null（如果没有更多的目录项要读取）
- dir.close()：不传入 callabck 函数则返回 Promise，关闭目录的底层资源句柄

#### fs.Dirent

- dirent.name
- dirent.isDirectory()
- dirent.isFile()
- dirent.isSymbolicLink()

#### fs.opendir 打开目录

`fs.opendir(path[, options], callback)` 打开一个目录，返回 fs.Dir 对象

```javascript
const fs = require('fs/promises');

async function print(path) {
  const dir = await fs.opendir(path);
  for await (const dirent of dir) {
    // 打印文件名或目录中的子目录名
    console.log(dirent.name);
  }
}
print('./').catch(console.error);
```

可以通过 dir.read() 迭代 dir

```javascript
const fs = require('fs/promises');

async function print(path) {
  const dir = await fs.opendir(path);
  let dirent = await dir.read();
  while (dirent) {
    console.log(dirent.name);
    dirent = await dir.read();
  }

  dir.close();
}
print('./').catch(console.error);
```

#### fs.readdir 读取目录中的文件列表

`fs.readdir(path[, options], callback)` 读取目录的内容，回调有两个参数 (err, files)，其中 **files 是目录中的文件名的数组**（不包括 '.' 和 '..'）

options

- encoding：默认值 utf8，如果 encoding 设置为 'buffer'，则返回的文件名是 Buffer 对象
- withFileTypes：默认值 false，设置为 true 后回调函数 files 数组将包含 fs.Dirent 对象

```javascript
const fs = require('fs/promises');

async function print(path) {
  const files = await fs.readdir(path);
  for (const file of files) {
    console.log(file);
  }
}
print('./').catch(console.error);
```

#### fs.mkdir 创建目录

`fs.mkdir(path[, options], callback)` 创建目录

options

- recursive：默认值 false，设置为 true 时候相当于命令 `mkdir -p` 会把不存在的目录创建
- mode：默认值 0o777，Windows 不支持

```javascript
// 创建 /tmp/a/apple 目录，无论是否存在 /tmp 和 /tmp/a 目录。
fs.mkdir('/tmp/a/apple', { recursive: true }, err => {
  if (err) throw err;
});
```

使用`fs.mkdir()`或`fs.mkdirSync()`创建一个新文件夹：

```js
const dirname = './why';

if (!fs.existsSync(dirname)) {
  fs.mkdir(dirname, (err) => {
    console.log(err);
  })
}
```

#### fs.rmdir 移除目录

`fs.rmdir(path[, options], callback)` fs.rmdir 用于删除文件夹

options

- recursive：默认值 false，如果为 true，则执行递归的目录删除。在递归模式中，如果 path 不存在则不报告错误，并且在失败时重试操作
- retryDelay：默认值 100，出现异常后重试之间等待毫秒数。如果 recursive 选项不为 true，则忽略此选项
- maxRetries：默认值为 0，表示出现异常后重试的次数，如果遇到 EBUSY、 EMFILE、 ENFILE、 ENOTEMPTY 或 EPERM 错误，则 Node.js 将会在每次尝试时以 retryDelay 毫秒的线性回退来重试该操作。 如果 recursive 为 false，则忽略此选项

```javascript
const fs = require('fs');

fs.rmdir('./tmp', { recursive: true }, err => console.log);
```

之前 rmdir 只能删除空的文件夹，现在可以连同文件一起删除了。

#### fs.rename 目录 / 文件重命名

```js
fs.rename('./why', './coder', err => {
  console.log(err);
})
```

### 获取所有文件夹和文件

```js
// 读取文件夹
function readFolders(folder) {
  fs.readdir(folder, {withFileTypes: true} ,(err, files) => {
    files.forEach(file => {
      if (file.isDirectory()) {
        const newFolder = path.resolve(dirname, file.name);
        readFolders(newFolder);
      } else {
        // 打印所有子目录或文件的文件名  
        console.log(file.name);
      }
    })
  })
}

readFolders(dirname);
```

### 获取目录中所有文件的绝对路径路径

```js
const path = require('path')
const fs = require('fs')
/**
 * 递归获取指定目录中的所有文件的绝对路径路径
 * @param {string} dir 目标目录
 * @param {string[]} 
 * @returns {string[]} 文件绝对路径数组
 */
function getDirFiles(dir) {
    let result = []
    let files = fs.readdirSync(dir, { withFileTypes: true })
    files.forEach(file => {
        const filepath = path.join(dir, file.name)
        if (file.isFile()) {
            result.push(filepath)
        } else if (file.isDirectory()) {
            result.push(...getDirFiles(filepath))
        }
    })
    return result;
}
```

### 某个目录下所有文件复制到新的目录

![Snipaste_2023-04-25_15-14-00](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682406853218-e75a1339-0a28-418a-98a5-21bd019c85a9.png)

### 文件批量重命名

使用 Node.js 写一个目录下批量重命名的命令行程序，目录路径为绝对路径并且由用户提供，

重命名规则：用户输入参数1为文件名称，查找目录下是否有文件名称部分匹配参数1的文件，没有则提示用户未找到匹配的文件名称。

用户输入参数2为新的文件名，将该目录下所有符合参数1文件名的文件部分替换为新的文件名。

package.json

```json
{
  "name": "filerenamedemo",
  "version": "1.0.0",
  "description": "",
  "exports": "./index.js",
  "engines": {
    "node": ">=14.16"
  },
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "inquirer": "^9.2.0"
  }
}
```

index.js

```js
import fs from 'fs'
import path from 'path'
import inquirer from 'inquirer';

function rename() {
  // 使用 inquirer 获取用户输入
  inquirer
    .prompt([
      {
        type: 'input',
        name: 'dest',
        message: "请输入目录路径（绝对路径，不能有 '' 号）：",
        validate: function (input) {
          return new Promise(function (resolve, reject) {
            fs.access(input, fs.constants.F_OK | fs.constants.R_OK, function (err) {
              if (err) {
                reject(new Error('🚨目录路径不正确或不可读，请重新输入'));
              } else {
                resolve(true);
              }
            });
          });
        }
      },
      {
        type: 'input',
        name: 'oldName',
        message: '请输入要替换的文件名：',
        validate: function (input) {
          if (!input.length) {
            return '🚨替换的文件名不能为空，请重新输入';
          } else {
            return true;
          }
        }
      },
      {
        type: 'input',
        name: 'newName',
        message: '请输入新的文件名（不填则为空）：'
      },
    ])
    .then(({ dest, oldName, newName }) => {
      // 读取目录下的文件列表
      fs.readdir(dest, { withFileTypes: true }, (err, files) => {
        if (err) {
          console.error('🚨' + err);
          return;
        }
        let renamedFiles = 0; // 记录重命名的文件数量
        // 遍历文件列表，找到需要重命名的文件
        files.forEach((file) => {
          if (file.isFile() && file.name.includes(oldName)) {
            const oldPath = path.join(dest, file.name);
            const newPath = path.join(dest, file.name.replace(oldName, newName));

            try {
              // 重命名文件
              fs.renameSync(oldPath, newPath);
              console.log(`📝 文件 ${file.name} 重命名成功`);
              renamedFiles++;
            } catch (err) {
              console.error(`🚨📝 文件 ${file.name} 重命名失败：${err}`);
            }
          }
        });

        // 输出结果
        if (renamedFiles === 0) {
          console.log('🤖️：当前目录下无法找到要替换的文件名，请检查替换的文件名');
          rename()
        } else {
          console.log('\n🤖️：报告！任务完成！\n');
          console.log(`🎉 ✅ 共重命名 ${renamedFiles} 个文件`);
        }
      });
    });
}

rename()
```

Node 原生

```js
const fs = require('fs');
const path = require('path');
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

// 获取用户输入的参数
rl.question('请输入要重命名的文件名：', (oldName) => {
  rl.question('请输入新的文件名：', (newName) => {
    // 获取要重命名的目录路径
    rl.question('请输入要重命名的目录路径：', (dirPath) => {
      // 获取目录下的所有文件
      fs.readdir(dirPath, (err, files) => {
        if (err) throw err;
        let matchFiles = files.filter(file => file.includes(oldName));
        if (matchFiles.length === 0) {
          console.log(`未找到名称包含"${oldName}"的文件`);
          rl.close();
        } else {
          // 遍历匹配到的文件，进行重命名
          matchFiles.forEach(matchFile => {
            let oldPath = path.join(dirPath, matchFile);
            let newPath = path.join(dirPath, matchFile.replace(oldName, newName));
            fs.rename(oldPath, newPath, (err) => {
              if (err) throw err;
              console.log(`${matchFile} 重命名为 ${path.basename(newPath)}`);
            });
          });
          rl.close();
        }
      });
    });
  });
});
```

### 合并指定目录下的所有文件的内容

index.js

```js
const process = require('process')
const path = require('path')
const fs = require('fs')

// 传入的目录
const targetDir = process.argv[2]
// 忽略的内容
const ignore = ['node_modules', '.git', 'dist',
 'ignore', 'README.md', '.lock', '.png','docs','.eslintrc.js',
 '.env','LICENSE','tsconfig.json','.github','_tests_']

const files = getDirFiles(targetDir, ignore)

/**
 * 递归获取指定目录中的所有文件的绝对路径路径
 * @param {string} dir 目标目录
 * @param {string[]} 
 * @returns {string[]} 文件绝对路径数组
 */
function getDirFiles(dir, exclude = []) {
    let result = []
    let files = fs.readdirSync(dir, { withFileTypes: true })
    files.forEach(file => {
        const filepath = path.join(dir, file.name)
        const isExclude = exclude.some(v => {
            return filepath.endsWith(v)
        })
        if (!isExclude) {
            if (file.isFile()) {
                result.push(filepath)
            } else if (file.isDirectory()) {
                result.push(...getDirFiles(filepath, exclude))
            }
        }
    })
    return result;
}

mergeFile(files)

/**
 * 内容并入一个文件中
 * @param {string[]} files 
 */
function mergeFile(files) {
    // 写入的目标文件(时间戳命名的 txt)
    const writeFilepath = path.join(__dirname, `${Date.now()}.txt`)
    files.forEach(f => {
        // 文件中的内容
        const txt = fs.readFileSync(f, { encoding: 'utf-8' })
        // 文件的相对路径
        const dir = f.slice(targetDir.length + 1)

        // 追加内容的方式
        fs.appendFileSync(writeFilepath, `${dir}\n`)
        fs.appendFileSync(writeFilepath, `${txt}\n\n`)
    })
    console.log('ok', files.length, '个文件');
    console.log(files);
}
```

使用

```sh
node index.js /project/a/b
```

### 读取 Chrome 书签

start.js

解析 html 书签为树级结构的 JSON 文件

```js
const fs = require('fs');
const bookmarkHtml = fs.readFileSync('bookmarks.html', 'utf-8');
const { JSDOM } = require('jsdom');
const dom = new JSDOM(bookmarkHtml);
const document = dom.window.document;

function parseBookmarkNode(node, id, parentId) {
  const bookmark = {};
  if (node.nodeName === 'A') {
    bookmark.id = id;
    bookmark.parentId = parentId;
    bookmark.title = node.innerHTML;
    bookmark.url = node.getAttribute('href');
    if (node.getAttribute('icon') === null) {
      bookmark.icon = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAACXBIWXMAAAsTAAALEwEAmpwYAAABgElEQVR4nM3Qu0tCYRjHcf+BoqEhutBFcfFCqATpUmewLBSxApcchIKmapOmyKkLFF3JwYJSGmwyCLpAU0WjtVmW1jkd9ZhmpkbZLzyBcMhTuvXAd3lf3g8Pr0Dwb8ZIImCkgJIiEfgJUPwPhuh32CIZzllZQCwdBLIBDlIWcJ6kwaSD7CZ/AvpQDq1HcSiO4zCEPnnRokDvXQ4SbxTCnUc26R7DgoQvA8KX/R3oCXxA7AmjcYvkJPbQkO8zkHoZqM9S/ECzm0Kd875oTdsk5LsPsE1swjUyBYd9BYysrZ4D1KwHwVfDqh8n5jEkCANiGh2iKgK0RB2nW5SyAlC9fAu+RsedSHb3I7PmRNxkQXrDhYiiA5RQdVAAquZvwJfDameBpNmKXDSG1OwSwlINSJEqUQAq566pihk/ijU5vMACWbcHqelFvJ1esBuQIuXlj88sNq8Gc22yq+8poTV9/0G7lt2AEqoGSwLy86IbkCU69Ycxje45oiSuwhKNJX/xBb3f0Y3M4X/YAAAAAElFTkSuQmCC'
    } else {
      bookmark.icon = node.getAttribute('icon');
    }
  } else if (node.nodeName === 'H3') {
    bookmark.id = id;
    bookmark.parentId = parentId;
    bookmark.title = node.innerHTML;
    bookmark.icon = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAiUlEQVR4nGNgGBTg/yoG5v8LGDz/z2cIhePFDHzEGzCfofn/Aob/KHg+wzGiDfm/QeDo/xNq//+f0iAOH1f793+XWBvCgP2yj4jWDMP7ZB8iDDikeIFkAw4pXkAYcEpjFckGnNJYNWqABnIYqDeRHIgn1RtRk/JJNY//J9VDicNqHiA9RCd1mgIAmj+mIQ1gJe8AAAAASUVORK5CYII='
    bookmark.children = [];
    let sibling = node.nextSibling;
    while (sibling) {
      if (sibling.nodeName === 'DL') {
        bookmark.children = parseBookmarkList(sibling, id);
        break;
      }
      sibling = sibling.nextSibling;
    }
  }
  return bookmark;
}

function parseBookmarkList(list, parentId) {
  const bookmarks = [];
  const nodes = list.children;
  for (let i = 0; i < nodes.length; i++) {
    const node = nodes[i];

    if (node.nodeName === 'DT') {
      const id = Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);
      const bookmarkNode = parseBookmarkNode(node.firstChild, id, parentId);
      bookmarks.push(bookmarkNode);
      if (bookmarkNode.children) {
        const children = parseBookmarkList(node.lastChild, id);
        bookmarks.push(...children);
      }
    }
  }
  return bookmarks;
}

function addLevelAndId(bookmarkNodes, level, parentId) {
  for (let i = 0; i < bookmarkNodes.length; i++) {
    const bookmarkNode = bookmarkNodes[i];
    bookmarkNode.level = level;
    bookmarkNode.parentId = parentId;
    if (bookmarkNode.children) {
      addLevelAndId(bookmarkNode.children, level + 1, bookmarkNode.id);
    }
  }
}

const bookmarks = document.querySelector('DL');
const bookmarkTree = parseBookmarkList(bookmarks, null);
addLevelAndId(bookmarkTree, 0, null);

const json = JSON.stringify(bookmarkTree, null, 2);
fs.writeFileSync('bookmarks.json', json, 'utf8');
```

crawl.js

爬取 bookmarks.json 的 url 获取 html 里 `<meta name="description" content="">` content 的值放到 content.json

```js
const axios = require('axios');
const cheerio = require('cheerio');
const fs = require('fs');

const data = require('./bookmarks.json');

axios.defaults.timeout = 10000;
axios.defaults.retry = 3;
axios.defaults.retryDelay = 1000;

const urls = [];

const traverse = (node) => {
  if (node.url) {
    urls.push(node.url);
  }
  if (node.children) {
    for (let child of node.children) {
      traverse(child);
    }
  }
}

traverse(data[0]);

const chunks = [];
while (urls.length) {
  chunks.push(urls.splice(0, 100));
}

const getDescription = async (url) => {
  try {
    const response = await axios.get(url);
    if (response.data) {
      const $ = cheerio.load(response.data);
      const description = $('meta[name="description"]').attr('content');
      if (description) {
        return description;
      }
    }
  } catch (error) {
    console.error('getDescription Error', error);
  }
}

const scrapeUrls = async (urls) => {
  const scrapedData = [];
  try {
    const requests = urls.map(async (url) => {
      const description = await getDescription(url);
      scrapedData.push({ url, description });
    });

    await Promise.all(requests);
  } catch (error) {
    console.error('scrapeUrls Error', error);
  }
  return scrapedData;
};

(async () => {
  const scrapedData = [];

  for (let chunk of chunks) {
    const data = await scrapeUrls(chunk);
    scrapedData.push(...data);
  }

  fs.writeFileSync('content.json', JSON.stringify(scrapedData, null, 2), 'utf8');
})();
```

add.js

根据 content.json 里的 url 添加  description 到 bookmarks.json

```js
const fs = require('fs');

const content = require('./content.json');
const bookmarks = require('./bookmarks.json');

const addDescription = (node) => {
  if (node.url) {
    const match = content.find((item) => item.url === node.url);
    if (match && match.description) {
      // 去除转义字符和转义符号后的字母
      const str = match.description
      const unEscapedStr = str.replace(/[\'\"\\\/\b\f\n\r\t]/g, '');
      const unSymbolStr = unEscapedStr.replace(/[\@\#\$\%\^\&\*\{\}\:\"\L\<\>\? ]/g, '');
      // 去除实体字符
      const entities = {
        '&lt;': '<',
        '&gt;': '>',
        '&quot;': '"',
        '&apos;': "'",
        '&amp;': '&'
      }
      const noEntityStr = unSymbolStr.replace(/&\w+;/g, match => entities[match])
      const noCommentedStr = noEntityStr.replace(/\/\/.*$/gm, '')
      const noQuotedStr = noCommentedStr.replace(/["']/g, '')
      // 去除 HTML 标签
      const noTagStr = noQuotedStr.replace(/<[^>]+>/g, '')
      const filteredStr = noTagStr.replace(/process.env.NODE_ENV/g, '')
      node.description = filteredStr.trim();
    }
  }
  if (node.children) {
    node.children.forEach((child) => {
      addDescription(child);
    });
  }
};

bookmarks.forEach((bookmark) => {
  addDescription(bookmark);
});

fs.writeFileSync('bookmarksDesc.json', JSON.stringify(bookmarks, null, 2), 'utf8');
```

get.js

根据 id 匹配数据的 children

```js
const fs = require('fs');
const bookmarkTree = fs.readFileSync('bookmarksDesc.json', 'utf8');
function getChildrenById(bookmarkNodes, id) {
  // 遍历 bookmarkNodes 数组
  for (let i = 0; i < bookmarkNodes.length; i++) {
    const bookmarkNode = bookmarkNodes[i];
    // 如果当前节点的 id 匹配目标 id，返回该节点的 children 数组
    if (bookmarkNode.id === id) {
      return bookmarkNode.children;
    }
    // 如果当前节点有 children 属性，递归调用 getChildrenById 函数
    if (bookmarkNode.children) {
      const children = getChildrenById(bookmarkNode.children, id);
      // 如果找到目标节点，返回该节点的 children 数组
      if (children) {
        return children;
      }
    }
  }
  // 如果找不到目标节点，返回 null
  return null;
}
const children = getChildrenById(JSON.parse(bookmarkTree), "eedv1lle59cx8e4q5npjm");
const json = JSON.stringify(children, null, 2);
fs.writeFileSync('get.json', json, 'utf8');
```

根据 id 数组获取 bookmarksTest.json  部分的 json 数据

1、根据 id 匹配数据

```js
function findDataById(data, id) {
  // 遍历数据数组
  for (let i = 0; i < data.length; i++) {
    const item = data[i];
    // 如果当前项的 id 和传入的 id 匹配，则返回当前项
    if (item.id === id) {
      return item;
    }
    // 如果当前项有 children，则递归查找该 id 的数据
    if (item.children && item.children.length > 0) {
      const result = findDataById(item.children, id);
      // 如果找到了数据，则返回结果
      if (result) {
        return result;
      }
    }
  }
  // 如果遍历完整个数据数组都没有找到，则返回 null
  return null;
}
const children = findDataById(JSON.parse(bookmarkTree), "eedv1lle59cx8e4q5npjm");
const json = JSON.stringify(children, null, 2);
fs.writeFileSync('get.json', json, 'utf8');
```

2、根据多个 id 匹配数据

```js
function findOneDataById(data, id) {
  for (let i = 0; i < data.length; i++) {
    const item = data[i];
    if (item.id === id) {
      const result = { ...item };
      if (item.children) {
        const childrenResult = [];
        item.children.forEach(child => {
          const childResult = findDataById(data, child.id);
          if (childResult) {
            childrenResult.push(childResult);
          }
        });
        result.children = childrenResult;
      }
      return result;
    } else if (item.children) {
      const childResult = findDataById(item.children, id);
      if (childResult) {
        return childResult;
      }
    }
  }
  return null;
}

function findDataByIds(data, ids) {
  const result = [];
  for (let i = 0; i < ids.length; i++) {
    const id = ids[i];
    const item = findOneDataById(data, id);
    if (item) {
      result.push(item);
    }
  }
  return result;
}

const idArr = ['eedv1lle59cx8e4q5npjm', 'kr46lmanbgpltpybnd8bo', '7y702sc9rn3sgwehlpdgy', '4sy8blxx3dvn78v80kqvyb', 'verv9jp437la6u4l3kc8l6', 'm6n3ybf9pidglaj47nmex']
const data = findDataByIds(JSON.parse(bookmarkTree), idArr);
const json = JSON.stringify(data, null, 2);
fs.writeFileSync('get.json', json, 'utf8');
```

递归删除 id

```js
function removeIds(obj) {
  if (Array.isArray(obj)) {
    obj.forEach(removeIds);
  } else if (typeof obj === 'object' && obj !== null) {
    delete obj.id;
    for (const key in obj) {
      removeIds(obj[key]);
    }
  }
}

removeIds(JSON.parse(bookmarkTree));
```

循环删除 id

```js
function removeIdsIterative(obj) {
  const stack = [obj];
  while (stack.length > 0) {
    const current = stack.pop();
    delete current.id;
    if (Array.isArray(current.children)) {
      current.children.forEach(child => stack.push(child));
    } else if (typeof current === 'object' && current !== null) {
      for (const key in current) {
        if (typeof current[key] === 'object' && current[key] !== null) {
          stack.push(current[key]);
        }
      }
    }
  }
}

removeIdsIterative(JSON.parse(bookmarkTree));
```

get2.js

添加 cover 字段（网页截图）

```js
import fs from 'fs';
import path from 'path';
// import images from 'images';

const bookmarkTree = fs.readFileSync('bookmarksTest.json', 'utf8');

const level = [2, 3, 4, 5, 6]
// const filePath = process.cwd() + `/images/`
// const size = 300 // 等比缩放到300像素宽
// const quality = 90 // 压缩质量:1-100，质量越小图片占用空间越小

// async function compress(id) {
//   images.setLimit(30000, 30000);
//   // 保存截图的路径及文件名
//   const path = filePath + `${id}.png`
//   // 保存缩略图的文件名，路径及名称和截图一样，只是把后缀改了
//   const thumbPath = path.replace('.png', '.jpg')

//   // 压缩图片
//   if (thumbPath) {
//     try {
//       await images(path).size(+size || 300).save(thumbPath, { quality: +quality || 70 })
//     } catch (error) {
//       console.error(`${path} error: ${error.message}`)
//     }
//   }
// }

function copy(fileName) {
  const imagesDir = './images';
  const staticDir = './static';
  let files = fs.readdirSync(imagesDir, { withFileTypes: true });
  files.forEach((file) => {
    if (file.isFile() && file.name === fileName) {
      const filePath = path.join(imagesDir, file.name);
      const readStream = fs.createReadStream(filePath);
      const writeStream = fs.createWriteStream(path.join(staticDir, file.name));

      readStream.pipe(writeStream);

      writeStream.on('finish', () => {
        console.log(`File ${file.name} copied to ${staticDir}`);
      });
    }
  });
}

function getChildrenById(bookmarkNodes, id) {
  // 遍历 bookmarkNodes 数组
  for (let i = 0; i < bookmarkNodes.length; i++) {
    const bookmarkNode = bookmarkNodes[i];
    // 如果当前节点的 id 匹配目标 id，返回该节点的 children 数组
    if (bookmarkNode.id === id && level.includes(bookmarkNode.level)) {
      bookmarkNode.children.forEach(item => {
        // compress(item.id)
        item.cover = `./images/${item.id}.png`
        copy(`${item.id}.png`)
      });
      console.log('successfully!');
      return bookmarkNode.children;
    } else {
      if (bookmarkNode.id === id) {
        return bookmarkNode.children;
      }
    }
    // 如果当前节点有 children 属性，递归调用 getChildrenById 函数
    if (bookmarkNode.children) {
      const children = getChildrenById(bookmarkNode.children, id);
      // 如果找到目标节点，返回该节点的 children 数组
      if (children) {
        return children;
      }
    }
  }
  // 如果找不到目标节点，返回 null
  return null;
}
const children = getChildrenById(JSON.parse(bookmarkTree), "eedv1lle59cx8e4q5npjm");
const json = JSON.stringify(children, null, 2);
fs.writeFileSync('get.json', json, 'utf8');
```

遍历 URL 获取网页截图

```js
import fs from 'fs';
import pLimit from 'p-limit';
import puppeteer from 'puppeteer';
import images from 'images';
import { Cluster } from 'puppeteer-cluster';

const size = 300 // 等比缩放到300像素宽
const quality = 90 // 压缩质量:1-100，质量越小图片占用空间越小

const filePath = process.cwd() + `/images/`

const bookmarks = JSON.parse(fs.readFileSync('bookmarksTest.json', 'utf8'));

const IMAGES_DIR = 'images';
const CONCURRENCY_LIMIT = 10; // 同时进行截图的最大并发数量

async function compress(id) {
  // 保存截图的路径及文件名
  const path = filePath + `${id}.png`
  // 保存缩略图的文件名，路径及名称和截图一样，只是把后缀改了
  const thumbPath = path.replace('.png', '.jpg')

  // 压缩图片
  if (thumbPath) {
    await images(path).size(+size || 300).save(thumbPath, { quality: +quality || 70 })
  }
}

async function removePng(id) {
  await fs.access(`./${IMAGES_DIR}/${id}.png`, function (err) {
    if (err) throw err;
    fs.unlink(`./${IMAGES_DIR}/${id}.png`, errMessage => {
      if (errMessage) throw errMessage;
      console.log(`${id}.png 已成功删除`);
    })
  });
}

// 创建 images 目录
if (!fs.existsSync(IMAGES_DIR)) {
  fs.mkdirSync(IMAGES_DIR);
}

// 遍历 JSON 数据并提取 URL
function extractUrls(data) {
  const urls = [];

  for (const item of data) {
    if (item.url) {
      urls.push({ id: item.id, url: item.url });
    }
    if (item.children) {
      urls.push(...extractUrls(item.children));
    }
  }

  return urls;
}

async function generateScreenshot(browser, id, url) {
  const page = await browser.newPage();
  await page.goto(url, { waitUntil: 'networkidle2' });
  await page.screenshot({ path: `${IMAGES_DIR}/${id}.png`, fullPage: true });
  await compress(id);
  await removePng(id);
  await page.close();
}

async function main() {
  const browser = await puppeteer.launch();
  const urls = extractUrls(bookmarks);
  const limit = pLimit(CONCURRENCY_LIMIT);

  const tasks = urls.map(({ id, url }) =>
    limit(() => generateScreenshot(browser, id, url).catch((error) => console.error(`Failed to capture screenshot for ${url}: ${error.message}`)))
  );

  await Promise.all(tasks);
  await browser.close();
}

main().catch((error) => {
  console.error(`Error: ${error.message}`);
  process.exit(1);
});
```



```js
import fs from 'fs';
import pLimit from 'p-limit';
import puppeteer from 'puppeteer';
import images from 'images';

const size = 300 // 等比缩放到300像素宽
const quality = 90 // 压缩质量:1-100，质量越小图片占用空间越小

const filePath = process.cwd() + `/images/`

const bookmarks = JSON.parse(fs.readFileSync('bookmarksTest.json', 'utf8'));

const IMAGES_DIR = 'images';
const CONCURRENCY_LIMIT = 10; // 同时进行截图的最大并发数量

async function compress(id) {
  // 保存截图的路径及文件名
  const path = filePath + `${id}.png`
  // 保存缩略图的文件名，路径及名称和截图一样，只是把后缀改了
  const thumbPath = path.replace('.png', '.jpg')

  // 压缩图片
  if (thumbPath) {
    await images(path).size(+size || 300).save(thumbPath, { quality: +quality || 70 })
  }
}

async function handleImage() {
  try {
    const files = await fs.promises.readdir(IMAGES_DIR);

    for (const file of files) {
      if (path.extname(file) === '.png') {
        await compress(file.name);
        await fs.promises.unlink(path.join(IMAGES_DIR, file));
        console.log(`Deleted file: ${file}`);
      }
    }

    console.log('All .png files are deleted successfully!');
  } catch (err) {
    console.error(err);
  }
}

// 创建 images 目录
if (!fs.existsSync(IMAGES_DIR)) {
  fs.mkdirSync(IMAGES_DIR);
}

// 遍历 JSON 数据并提取 URL
function extractUrls(data) {
  const urls = [];

  for (const item of data) {
    if (item.url) {
      urls.push({ id: item.id, url: item.url });
    }
    if (item.children) {
      urls.push(...extractUrls(item.children));
    }
  }

  return urls;
}

async function generateScreenshot(browser, id, url) {
  const page = await browser.newPage();
  await page.goto(url, { waitUntil: 'networkidle2' });
  await page.screenshot({ path: `${IMAGES_DIR}/${id}.png`, fullPage: true });
  await page.close();
}

async function main() {
  const browser = await puppeteer.launch();
  const urls = extractUrls(bookmarks);
  const limit = pLimit(CONCURRENCY_LIMIT);

  const tasks = urls.map(({ id, url }) =>
    limit(() => generateScreenshot(browser, id, url).catch((error) => console.error(`Failed to capture screenshot for ${url}: ${error.message}`)))
  );

  await Promise.all(tasks);
  await browser.close();
}

main().then(() => {
  handleImage();
}).catch((error) => {
  console.error(`Error: ${error.message}`);
  process.exit(1);
});
```

download.js

```js
import fs from 'fs';
import pLimit from 'p-limit';
import puppeteer from 'puppeteer';
import images from 'images';

const size = 300 // 等比缩放到300像素宽
const quality = 90 // 压缩质量:1-100，质量越小图片占用空间越小

const filePath = process.cwd() + `/images/`

const bookmarks = JSON.parse(fs.readFileSync('bookmarksTest.json', 'utf8'));

const IMAGES_DIR = 'images';
const CONCURRENCY_LIMIT = 10; // 同时进行截图的最大并发数量

async function compress(id) {
  // 保存截图的路径及文件名
  const path = filePath + `${id}.png`
  // 保存缩略图的文件名，路径及名称和截图一样，只是把后缀改了
  const thumbPath = path.replace('.png', '.jpg')

  // 压缩图片
  if (thumbPath) {
    await images(path).size(+size || 300).save(thumbPath, { quality: +quality || 70 })
  }
}

async function handleImage() {
  try {
    const files = await fs.promises.readdir(IMAGES_DIR);

    for (const file of files) {
      if (path.extname(file) === '.png') {
        await compress(file.name);
        await fs.promises.unlink(path.join(IMAGES_DIR, file));
        console.log(`Deleted file: ${file}`);
      }
    }

    console.log('All .png files are deleted successfully!');
  } catch (err) {
    console.error(err);
  }
}

// 创建 images 目录
if (!fs.existsSync(IMAGES_DIR)) {
  fs.mkdirSync(IMAGES_DIR);
}

// 遍历 JSON 数据并提取 URL
function extractUrls(data) {
  const urls = [];

  for (const item of data) {
    if (item.url) {
      urls.push({ id: item.id, url: item.url });
    }
    if (item.children) {
      urls.push(...extractUrls(item.children));
    }
  }

  return urls;
}

async function generateScreenshot(browser, id, url) {
  const page = await browser.newPage();
  await page.goto(url, { waitUntil: 'networkidle2' });
  await page.screenshot({ path: `${IMAGES_DIR}/${id}.png`, fullPage: true });
  await page.close();
}

async function main() {
  const browser = await puppeteer.launch();
  const urls = extractUrls(bookmarks);
  const limit = pLimit(CONCURRENCY_LIMIT);

  const tasks = urls.map(({ id, url }) =>
    limit(() => generateScreenshot(browser, id, url).catch((error) => console.error(`Failed to capture screenshot for ${url}: ${error.message}`)))
  );

  await Promise.all(tasks);
  await browser.close();
}

main().then(() => {
  handleImage();
}).catch((error) => {
  console.error(`Error: ${error.message}`);
  process.exit(1);
});
```

前端

components\BookMarkTree.vue

```vue
<template>
  <el-tree :data="treeData" :props="treeProps">
    <template #default="{ node, data }">
      <span class="custom-tree-node">
        <a class="link" :href="data.url" target="_blank">
          <img :src="data.icon" v-if="data.icon" />
          {{ data.title }}
        </a>
      </span>
      </template>
  </el-tree>
</template>

<script setup>
let props = defineProps({
   bookmarks: {
     type: Array,
     required: true,
   },
 });
const buildTree = (bookmarkNodes) => {
  return bookmarkNodes.map((node) => {
    const treeNode = {
      id: node.id,
      parentId: node.parentId,
      title: node.title,
      icon: node.icon,
      url: node.url,
      level: node.level,
      description: node.description
    };
    if (node.children && node.children.length > 0) {
      treeNode.children = buildTree(node.children);
    }
    return treeNode;
  });
} 
const treeData = computed(() => buildTree(props.bookmarks));
const treeProps = computed(() => ({ label: 'title', children: 'children'}));

function getChildrenById(bookmarkNodes, id) {
  // 遍历 bookmarkNodes 数组
  for (let i = 0; i < bookmarkNodes.length; i++) {
    const bookmarkNode = bookmarkNodes[i];
    // 如果当前节点的 id 匹配目标 id，返回该节点的 children 数组
    if (bookmarkNode.id === id) {
      return bookmarkNode.children;
    }
    // 如果当前节点有 children 属性，递归调用 getChildrenById 函数
    if (bookmarkNode.children) {
      const children = getChildrenById(bookmarkNode.children, id);
      // 如果找到目标节点，返回该节点的 children 数组
      if (children) {
        return children;
      }
    }
  }
  // 如果找不到目标节点，返回 null
  return null;
}
const children = getChildrenById(props.bookmarks, "eedv1lle59cx8e4q5npjm");
console.log('children : ', JSON.stringify(children));
</script>

<style lang="scss" scoped>
  .link {
    text-decoration: none;
    margin-left: 8px;
    &:hover {
      color: #409eff;
    }
  }
</style>
```

pages\index.vue

```vue
<template>
  <div>
    <BookMarkTree :bookmarks="bookmarks" />
  </div>
</template>

<script setup>
import bookmarkData from "../data/bookmarksDesc.json";
const bookmarks = reactive(bookmarkData);
</script>

<style lang="scss" scoped>
</style>
```

components\BookMarkGrid.vue

```vue
<template>
  <div class="view">
    <template v-for="item in bookmark" :key="item.id">
      <div class="block">
        <div class="title">
          <div class="title-padding">{{ item.title }}</div>
        </div>
      </div>
      <div class="gallery-view">
        <div class="collection-view">
          <div v-for="itemX in item.children" :key="itemX.id" class="item">
            <a :href="itemX.url" target="_blank" class="item-header">
              <div class="logo-wrapper"><img v-if="itemX.icon" :src="itemX.icon" class="logo"/> </div>
              <div class="desc">{{ itemX.title }}</div>
            </a>
          </div> 
        </div> 
      </div>
    </template>
  </div>
</template>

<script setup lang="ts">
interface IBookMarkNodes {
  id: string,
  parentId: string,
  title: string,
  url: string,
  icon: string,
  level: number
  description: string,
  cover: string,
  children?: IBookMarkNodes[]
}
let props = defineProps({
  bookmark: {
    type: Array as PropType<IBookMarkNodes[]>,
    require: true
  }
});
</script>

<style lang="scss" scoped>
.block, .gallery-view {
  background-color: #fff;
}
.view {
  height: 100%;
}
.view::-webkit-scrollbar {
  width : 10px;  
  height: 10px;
  background: transparent;
}
.view::-webkit-scrollbar-thumb {
  background: #D3D1CB;
}
.view::-webkit-scrollbar-track {
  background   : #EDECE9
}
.view::-webkit-scrollbar-thumb:hover {
	background:#AEACA6;
}
.block {
  width: 100%;
  min-height: 40px;
}
.title {
  display: flex; 
  align-items: center; 
  color: rgb(55, 53, 47);
  font-size: 14px;
  width: 100%; 
  height: 40px; 
  box-shadow: rgb(233, 233, 231) 0px -1px 0px inset;
}
.title-padding {
  padding: 0 0 0 8px;
}
.gallery-view {
  box-sizing: border-box;
  padding-bottom: 20px;
  padding-left: 40px;
  padding-right: 40px;
  min-width: calc(100% - 192px);
  margin: 0 0 20px 0;
}
.collection-view {
  display: grid; 
  height: 100%;
  position: relative; 
  grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); 
  gap: 32px; 
  padding-top: 16px; 
  padding-bottom: 4px;
}
.item {
  border-radius: 6px;
  width: 100%;
  padding: 10px;
  overflow: hidden;
  border: 1px solid #e4ecf3;
  box-shadow: 1px 2px 3px #f2f6f8;
  &:hover {
    transform: translateY(-10px);
    transition: .5s;
  }
}

a {
  text-decoration: none;
  cursor: pointer;
}
.item-header {
  display: flex;
  height: 100%;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
}
.logo-wrapper{
  display: flex;
  align-items: center;
  height: 50px;
}
.logo {
  width: 20px;
  height: 20px;
  border-radius: 50%;
}
.desc {
  box-shadow: rgb(233, 233, 231) 0px 1px 0px inset;
  padding: 15px;
  font-size: 14px;
  color: rgb(107, 114, 128);
  width: 100%;
  height: 40px;
  text-align: center;
  overflow: hidden;
  text-overflow: ellipsis;
  word-break: break-all;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 4; // 需要显示的行数  
  -webkit-box-orient: vertical;
}
</style>
```

pages\index.vue

```vue
<template>
  <div class="book-mark-app">
    <BookMarkGrid :bookmark="bookmark"></BookMarkGrid>
  </div>
</template>

<script setup>
import bookmarkData from "../data/get.json";
const bookmark = reactive(bookmarkData);
</script>

```

app.vue

```vue
<template>
  <div class="app">
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>

<style lang="scss" scoped>
.app {
  height: 100%;
  padding: 0 96px; 
  overflow: hidden;
  position: relative;
  background-color: #f3f6f8;
}
@media screen and (max-width: 1200px) {
  .app {
    padding: 0 96px;
  }
}
@media screen and (max-width: 992px) {
  .app {
    padding: 0 24px; 
  }
}
@media screen and (max-width: 768px) {
  .app {
    padding: 0 24px; 
  }
}
@media screen and (max-width: 576px) {
  .app {
    padding: 0 24px; 
  }
}
</style>

<style>
body {
  margin: 0;
  padding: 0;
}
</style>
```

## 内置模块 Events

### 引入 Events 模块

```js
const EventEmitter = require("events");
```

不要在事件监听器中抛出异常，这可能会导致 Node.js 进程崩溃。

可以使用 `try...catch` 语句来捕获异常，以避免程序崩溃。

on 事件监听每次添加 listener 不会检查是否添加过，多次调用 on 传入相同的 eventName 和 listener，会导致 listener 被添加多次

### 继承  EventEmitter

可以通过继承 EventEmitter 类来创建自定义的事件驱动程序。

注册 Application 实例,继承 EventEmitter 类,通过继承而来的 eventEmitter.on() 函数监听事件,eventEmitter.emit()触发事件

```js
class Application extends EventEmitter {}

const app = new Application()

//  监听hello事件
app.on('hello', data => {
  console.log(data) // hello nodeJs
})

//  触发hello事件
app.emit('hello', 'hello nodeJs')
```

### 事件监听

#### on

1. EventEmitter 实例会维护一个 listener 数组，每次 listener 默认会被添加到数组尾部
2. 每次添加 listener 不会检查是否添加过，多次调用 on 传入相同的 eventName 和 listener，会导致 listener 被添加多次

```js
const ee = new EventEmitter();
ee.on('foo', () => console.log('a'));
```

#### prependListener

```js
emitter.prependListener(eventName, listener)
```

通过 prependListener 可以把 listener 添加到 listener 数组头部

```js
const ee = new EventEmitter();
ee.prependListener('foo', () => console.log('a'));
```

#### once

如果希望 listener 被触发一次后就不再触发，可以使用 once 来绑定事件

```js
const ee = new EventEmitter();
ee.once('foo', () => console.log('a'));
```

#### setMaxListeners

在 Node.js v11.0.0 及更高版本中，

如果没有为 EventEmitter 实例设置任何监听器，那么会输出一个关于内存泄漏的警告信息。

可以使用 EventEmitter.defaultMaxListeners 属性来设置默认的最大监听器数量，

或者使用 EventEmitter.setMaxListeners() 方法来设置特定实例的最大监听器数量。

为 EventEmitter 实例设置最大监听器数量。

默认情况下，EventEmitter 实例最多只能添加 10 个监听器。

如果要监听更多的事件，可以使用该方法来增加最大监听器数量。

```js
const EventEmitter = require('events');
const myEmitter = new EventEmitter();

myEmitter.setMaxListeners(20);

for (let i = 0; i < 20; i++) {
  myEmitter.on('event', function() {
    console.log('触发了一个事件！');
  });
}

myEmitter.emit('event');
```

### 事件触发

使用 emit 方法可以按照 listener 注册的顺序，同步地调用每个注册到名为 eventName 的事件的监听器，并传入提供的参数

```js
const EventEmitter = require('events');
const myEmitter = new EventEmitter();

// 第一个监听器。
myEmitter.on('event', function firstListener() {
  console.log('第一个监听器');
});
// 第二个监听器。
myEmitter.on('event', function secondListener(arg1, arg2) {
  console.log(`第二个监听器中的事件有参数 ${arg1}、${arg2}`);
});
// 第三个监听器
myEmitter.on('event', function thirdListener(...args) {
  const parameters = args.join(', ');
  console.log(`第三个监听器中的事件有参数 ${parameters}`);
});

console.log(myEmitter.listeners('event'));

myEmitter.emit('event', 1, 2, 3, 4, 5);

// Prints:
// [
//   [Function: firstListener],
//   [Function: secondListener],
//   [Function: thirdListener]
// ]
// 第一个监听器
// 第二个监听器中的事件有参数 1、2
// 第三个监听器中的事件有参数 1, 2, 3, 4, 5
```

#### this 指向

当监听器函数被调用时， this 会被指向监听器所绑定的 EventEmitter 实例。

也可以使用 ES6 的箭头函数作为监听器,但 this 不会指向 EventEmitter 实例。

```js
const myEmitter = new MyEmitter();
myEmitter.on('event', function(a, b) {
  console.log(a, b, this, this === myEmitter);
  // 打印:
  //   a b MyEmitter {
  //     domain: null,
  //     _events: { event: [Function] },
  //     _eventsCount: 1,
  //     _maxListeners: undefined } true
});
myEmitter.emit('event', 'a', 'b');
```

```js
const myEmitter = new MyEmitter();
myEmitter.on('event', (a, b) => {
  console.log(a, b, this);
  // 打印: a b {}
});
myEmitter.emit('event', 'a', 'b');
```

#### 异步调用

`EventEmitter` 以注册的顺序同步地调用所有 listener，这样可以确保事件的正确排序，

listener 可以使用 `setImmediate()` 和 `process.nextTick()` 方法切换到异步的操作模式

```js
const myEmitter = new MyEmitter();
myEmitter.on('event', (a, b) => {
  setImmediate(() => {
    console.log('异步地发生');
  });
});
myEmitter.emit('event', 'a', 'b');
```

#### 事件卸载

##### off/removeListener

off 方法是 removeListener 方法的别名，用于清理事件绑定 `emitter.removeListener(eventName, listener)` 

```js
const callback = (stream) => {
  console.log('已连接');
};
server.on('connection', callback);
// ...
server.removeListener('connection', callback);
```

removeListener() 最多只会从监听器数组中移除一个监听器。

如果监听器被多次添加到指定 eventName 的监听器数组中，则必须多次调用 removeListener() 才能移除所有实例

##### removeAllListeners

```js
emitter.removeAllListeners([eventName])
```

移除指定的 eventName 事件的 listener，如果没有指定 eventName，则移除事件对象的所有 listener。

可以通过 `emitter.eventNames()` 获取事件对象上的 eventName 数组

### 基本使用

```js
// 1.创建发射器
const emitter = new EventEmitter();

// 2.监听某一个事件
// on是 addListener 的别名简写
emitter.on('click', (args) => {
  console.log("监听1到click事件", args);
})

const listener2 = (args) => {
  console.log("监听2到click事件", args);
}
emitter.on('click', listener2)

// 3.发出一个事件
setTimeout(() => {
  emitter.emit("click", "coderwhy", "james", "kobe");
  // off 方法是 removeListener 方法的别名
  emitter.off("click", listener2);
  emitter.emit("click", "coderwhy", "james", "kobe");
}, 2000);
```

### 获取 events 信息

```js
// 1.创建发射器
const emitter = new EventEmitter();

// 2.监听某一个事件
// addListener是on的alias简写
emitter.on('click', (args) => {
  console.log("监听1到click事件", args);
})

const listener2 = (args) => {
  console.log("监听2到click事件", args);
}
emitter.on('click', listener2)

emitter.on("tap", (args) => {
  console.log(args);
})

// 3.获取注册的事件
// 返回一个数组， EventEmitter 实例当前已注册的所有事件的名称。
console.log(emitter.eventNames());
// 返回指定事件的监听器数量。
console.log(emitter.listenerCount("click"));
// 返回指定事件的监听器数组。
console.log(emitter.listeners("click"));
```

## Buffer（缓冲区）

计算机中所有的内容：文字、数字、图片、音频、视频最终都会使用二进制来表示。

例如，我想存储 66 这个数字，计算机会先将数字 66 转化为二进制 01000010 表示，转换公式如下所示：

| 128  | 64   | 32   | 16   | 8    | 4    | 2    | 1    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 1    | 0    | 0    | 0    | 0    | 1    | 0    |

Buffer 用于读取或操作二进制数据流，作为 Node.js API 的一部分使用时无需 require，

用于操作网络协议、数据库、图片和文件 I/O 等一些需要大量二进制数据的场景。

Buffer 在创建时大小已经被确定且是无法调整的，在内存分配这块 Buffer 是由 C++ 层面提供而不是 V8 。

通常，数据的移动是为了处理或者读取它，并根据它进行决策。

伴随着时间的推移，每一个过程都会有一个最小或最大数据量。

如果数据到达的速度比进程消耗的速度快，那么少数早到达的数据会处于等待区等候被处理。

反之，如果数据到达的速度比进程消耗的数据慢，那么早先到达的数据需要等待一定量的数据到达之后才能被处理。

这里的等待区就指的缓冲区（Buffer），它是计算机中的一个小物理单位，通常位于计算机的 RAM 中。

举一个公共汽车站乘车的例子，通常公共汽车会每隔几十分钟一趟，在这个时间到达之前就算乘客已经满了，车辆也不会提前发车，

早到的乘客就需要先在车站进行等待。假设到达的乘客过多，后到的一部分则需要在公共汽车站等待下一趟车驶来。

在上面例子中的等待区公共汽车站，对应到我们的 Node.js 中也就是缓冲区（Buffer），

另外乘客到达的速度是我们不能控制的，我们能控制的也只有何时发车，

对应到我们的程序中就是我们无法控制数据流到达的时间，可以做的是能决定何时发送数据。

缓冲（Buffer）与缓存（Cache）的区别？

缓冲（Buffer）

缓冲（Buffer）是用于处理二进制流数据，将数据缓冲起来，它是临时性的，

对于流式数据，会采用缓冲区将数据临时存储起来，等缓冲到一定的大小之后在存入硬盘中。

视频播放器就是一个经典的例子，有时你会看到一个缓冲的图标，这意味着此时这一组缓冲区并未填满，

当数据到达填满缓冲区并且被处理之后，此时缓冲图标消失，你可以看到一些图像数据。

缓存（Cache）

缓存（Cache）我们可以看作是一个中间层，它可以是永久性的将热点数据进行缓存，使得访问速度更快，

例如我们通过 Memory、Redis 等将数据从硬盘或其它第三方接口中请求过来进行缓存，

目的就是将数据存于内存的缓存区中，这样对同一个资源进行访问，速度会更快，也是性能优化一个重要的点。

bit 是我们常说的比特，比特币就是以此命名的，

bit 是二进制的最小信息单位，1 bit 就是我们说的 1位，64 位操作系统 CPU一次能处理 2^64 位的数据。

Byte 被翻译为字节，是计量存储或者传输流量的单位，硬盘容量、网速等说的都是字节，

一个英文字符是一个字节，也就是我们说的 1B，中文字符通常是两个字节（Node.js 中使用三个字节）。

Buffer 看成是一个存储二进制的数组，这个数组中的每一项，可以保存 8  位二进制： 00000000，

为什么是 8 位呢？

8 位合在一起作为一个单元，这个单元称之为一个字节（byte）

1byte = 8bit，1kb=1024byte，1M=1024kb;

也就是说 Buffer 数组中的每一项相当于于一个字节的大小 。 

### 创建 Buffer

#### Buffer.from

Buffer.from 支持四种参数类型

- Buffer.from(string [, encoding])：返回一个包含给定字符串的 Buffer
- Buffer.from(buffer)：返回给定 Buffer 的一个副本 Buffer
- Buffer.from(array)：返回一个内容包含所提供的字节副本的 Buffer，数组中每一项是一个表示八位字节的数字，所以值必须在 0 ~ 255 之间，否则会取模
- Buffer.from(arrayBuffer)：返回一个与给定的 ArrayBuffer 共享内存的新 Buffer
- Buffer.from(object[, offsetOrEncoding[, length]])：取 object 的 valueOf 或 Symbol.toPrimitive 初始化 Buffer

```javascript
// 16 进制显示值

const buf1 = Buffer.from('test', 'utf-8'); // <Buffer 74 65 73 74>

const buf2 = Buffer.from(buf1); // <Buffer 74 65 73 74>，buf1 副本，修改 buf2 不会影响 buf1

const buf3 = Buffer.from([256, 2, 3]); // <Buffer 00 02 03>，超过 255 会取模

const arr = new Uint16Array(2);
arr[0] = 5000;
arr[1] = 4000;

// 和 arr 共享内存
const buf4 = Buffer.from(arr.buffer);
console.log(buf4); // <Buffer 88 13 a0 0f>

// 修改 arr
arr[1] = 6000;
console.log(buf4); // <Buffer 88 13 70 17> buf4 也受到影响

const buf = Buffer.from(new String('this is a test'));
// <Buffer 74 68 69 73 20 69 73 20 61 20 74 65 73 74>
```

#### Buffer.alloc(size [, fill [, encoding]])

分配一个大小为 size 字节的新 Buffer，如果 fill 为 undefined，则用 0 填充 Buffer

- `size <integer>` 新 Buffer 的所需长度
- `fill <string> | <Buffer> | <Uint8Array> | <integer>` 用于预填充新 Buffer 的值。默认值: 0
- `encoding <string>` 如果 fill 是一个字符串，则这是它的字符编码。默认值: utf8

```javascript
const buf = Buffer.alloc(5);
console.log(buf); // <Buffer 00 00 00 00 00>
```

如果指定了 fill，则分配的 Buffer 通过调用 ` buf.fill(fill)` 进行初始化。

```javascript
const buf = Buffer.alloc(5, 'a');
console.log(buf); // <Buffer 61 61 61 61 61>
```

#### Buffer.allocUnsafe(size)

分配一个大小为 size 字节的新 Buffer，allocUnsafe 执行速度比 alloc 快，但方法的名字听起来很不安全，确实也不安全

```javascript
const buf = Buffer.allocUnsafe(10);
console.log(buf); // 打印内容不确定
```

Buffer 模块会预分配一个内部的大小为[ Buffer.poolSize](https://nodejs.org/dist/latest-v12.x/docs/api/buffer.html#buffer_class_property_buffer_poolsize) 的 Buffer 实例，作为快速分配的内存池，用于使用 allocUnsafe() 创建新的 Buffer 实例

alloc 永远不会使用内部的 Buffer 池，而 allocUnsafe 在 size 小于或等于 Buffer.poolSize 的一半时将会使用内部的 Buffer池。 

当调用 allocUnsafe 时分配的内存段尚未初始化（不归零），这样分配内存速度很快，但分配到的内存片段可能包含旧数据。

如果在使用的时候不覆盖这些旧数据就可能造成内存泄露，虽然速度快，尽量避免使用。

#### Buffer.allocUnsafeSlow(size)

创建一个大小为 size 字节的新 Buffer，直接通过c++进行内存分配，不会进行旧值填充，

当分配的空间小于 4 KB的时候，allocUnsafe 会直接使用预分配的 Buffer，因此速度比 allocUnsafeSlow 要快，

当大于等于 4 KB的时候二者速度相差无异

```js
const bAllocUnsafe1 = Buffer.allocUnsafe(10);

console.log(bAllocUnsafe1); // <Buffer 49 ae c9 cd 49 1d 00 00 11 4f>
```

### Buffer 字符编码

如果我们希望将一个字符串放入到 Buffer 中，是怎么样的过程？

打印的时候 **Buffer 是一个个16 进制数字组成的数组**

![Snipaste_2023-04-26_15-04-58](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682493833428-f8aa0a08-c69d-4305-a263-f49052afa33e.png)

**Buffer 默认编码是：utf-8**

toString() 默认编码也是：utf-8

一个中文在 utf-8 下占用 3 个字节

一个数字或英文在 utf-8 下占用 1 个字节

通过使用字符编码，可实现 Buffer 实例与 JavaScript 字符串之间的相互转换，目前所支持的字符编码如下所示：

- 'ascii' - 仅适用于 7 位 ASCII 数据。此编码速度很快，如果设置则会剥离高位。
- 'utf8' - 多字节编码的 Unicode 字符。许多网页和其他文档格式都使用 UTF-8。
- 'utf16le' - 2 或 4 个字节，小端序编码的 Unicode 字符。支持代理对（U+10000 至 U+10FFFF）。
- 'ucs2' - 'utf16le' 的别名。
- 'base64' - Base64 编码。当从字符串创建 Buffer 时，此编码也会正确地接受 RFC 4648 第 5 节中指定的 “URL 和文件名安全字母”。
- 'latin1' - 一种将 Buffer 编码成单字节编码字符串的方法（由 RFC 1345 中的 IANA 定义，第 63 页，作为 Latin-1 的补充块和 C0/C1 控制码）。
- 'binary' - 'latin1' 的别名。
- 'hex' - 将每个字节编码成两个十六进制的字符。

![Snipaste_2023-04-26_15-04-38](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682493831454-dcbe670c-6274-4781-bc2b-d87b9ec2b62f.png)

### Buffer 拼接

Buffer.concat(list[, totalLength]) 方法可以把多个 Buffer 实例拼接为一个 Buffer 实例

```js
const buf1 = Buffer.from('a');
const buf2 = Buffer.from('b');
const buf3 = Buffer.from('c');

const buf = Buffer.concat([buf1, buf2, buf3]);
console.log(buf); // <Buffer 61 62 63>
```

### StringDecoder

在 NodeJS 中一个汉字由三个字节表示，如果我们处理中文字符的时候使用了不是3的倍数的字节数就会造成字符拼接乱码问题

```js
const buf = Buffer.from('中文字符串！');

for(let i = 0; i < buf.length; i+=5){
  var b = Buffer.allocUnsafe(5);
  buf.copy(b, 0, i);
  console.log(b.toString());
}
```

使用 string_decoder 模块可以解决这个问题

```js
const StringDecoder = require('string_decoder').StringDecoder;
const decoder = new StringDecoder('utf8');
const buf = Buffer.from('中文字符串！');
for(let i = 0; i < buf.length; i+=5){
  var b = Buffer.allocUnsafe(5);
  buf.copy(b, 0, i);
  console.log(decoder.write(b));
}
```

StringDecoder 在得到编码后，知道宽字节在 utf-8 下占3个字节，

所以在处理末尾不全的字节时，会保留到第二次 write()。

目前只能处理UTF-8、Base64 和 UCS-2/UTF-16LE。

### Buffer 和文件读取

![Snipaste_2023-04-26_16-02-32](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682496187089-c711311d-6e7f-4476-ad75-0642b87e458c.png)

### 其它 API

#### buf.write(string[, offset[, length]][, encoding])

写入指定字符串到 `Buffer` 实例中。

`string` 参数指定要写入的字符串，

`offset` 参数可选，用于指定写入的起始位置，默认为 0。

`length` 参数可选，用于指定要写入的字节数，默认为 `Buffer` 的长度减去 `offset`。

`encoding` 参数可选，用于指定 `string` 的编码，默认为 'utf8'。

```js
const buf = Buffer.alloc(10);

buf.write('hello', 0, 5);

console.log(buf);
// 输出：<Buffer 68 65 6c 6c 6f 00 00 00 00 00>
```

#### buf.slice([start[, end]])

创建一个新的 `Buffer` 实例，其中包含原始 `Buffer` 的副本，但仅包含指定的字节范围。

`start` 和 `end` 参数可选，用于指定要包含的字节范围，默认为整个 `Buffer`。

```js
const buf1 = Buffer.from('hello', 'utf8');
const buf2 = buf1.slice(0, 2);

console.log(buf2.toString());
// 输出：he
```

#### buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])

将 `Buffer` 实例的内容复制到另一个 `Buffer` 实例中。

`target` 参数指定目标 `Buffer`，`targetStart` 参数可选，用于指定目标 `Buffer` 的起始位置，默认为 0。

`sourceStart` 和 `sourceEnd` 参数可选，用于指定原始 `Buffer` 的字节范围，默认为整个 `Buffer`。

```js
const buf1 = Buffer.from('hello', 'utf8');
const buf2 = Buffer.alloc(10);

buf1.copy(buf2, 2);

console.log(buf2);
// 输出：<Buffer 00 00 68 65 6c 6c 6f 00 00 00>
```

#### buf.compare(target)

比较两个 `Buffer` 实例的内容。

如果 `buf` 的内容在字典序中排在 `target` 之前，则返回负数。

如果 `buf` 的内容在字典序中排在 `target` 之后，则返回正数。如果 `buf` 和 `target` 的内容相同，则返回 0。

```js
const buf1 = Buffer.from('hello', 'utf8');
const buf2 = Buffer.from('world', 'utf8');

console.log(buf1.compare(buf2));
// 输出：-1
```

#### buf.equals(otherBuffer)

比较两个 `Buffer` 实例的内容是否相同。如果相同，则返回 true，否则返回 false。

```js
const buf1 = Buffer.from('hello', 'utf8');
const buf2 = Buffer.from('hello', 'utf8');
const buf3 = Buffer.from('world', 'utf8');

console.log(buf1.equals(buf2));
// 输出：true

console.log(buf1.equals(buf3));
// 输出：false
```

#### Buffer.isBuffer(obj)

检查一个对象是否为 `Buffer` 实例。如果是，则返回 true，否则返回 false。

```js
const buf = Buffer.from('hello', 'utf8');

console.log(Buffer.isBuffer(buf));
// 输出：true

console.log(Buffer.isBuffer('hello'));
// 输出：false
```

## Stream（流）

谈论流时，有两个主要的方向：

- 实现（implementing）流

- 消费（consuming）流

### 什么是  stream

**流是数据的集合**

流水，源源不断的流动；

程序中的流也是类似的含义，

我们可以想象当我们从一个文件中读取数据时，文件的二进制（字节）数据会源源不断的被读取到我们程序中；

而这个一连串的字节，就是我们程序中的流。

总之它是会冒数据（以 `Buffer` 为单位），或者能够吸收数据的东西，它的本质就是让数据流动起来。可能看一张图会更直观：

![640](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1682511826404-342a310c-8231-4337-a2f2-959157aa65f7.jpeg)

**流不仅可以处理大容量的数据。它们也给了我们在代码中组合的能力。就像在我们可以通过导入其他一些更小的 Linux 命令那样组合出强大的 Linux 命令。**

**注意：`stream`不是 node.js 独有的概念，而是一个操作系统最基本的操作方式，只不过 node.js 有 API 支持这种操作方式。linux 命令的 | 就是`stream`。**

```bash
ls | grep *.js
```

命令的含义是 list 当前目录文件，把结果交给 grep 命令，按行筛选出拓展名是 js 的内容

使用 | 连接两条命令，把前一个命令的结果作为后一个命令的参数传入，

**这样数据像是水流在管道中传递，每个命令类似一个处理器，对数据做一些加工，因此 | 被称为“管道符”，这个处理过程就是流**。

```js
const ls = ... // ls 输出流
const grep = ... // grep 输入流
ls.pipe(grep)
```

**流，英文 Stream 是对输入输出设备的抽象，这里的设备可以是文件、网络、内存等。**

**流是有方向性的，当程序从某个数据源读入数据，会开启一个输入流，**

**这里的数据源可以是文件或者网络等，例如我们从 a.txt 文件读入数据。**

**相反的当我们的程序需要写入数据到指定数据源（文件、网络等）时，则开启一个输出流。**

我们可以直接通过 readFile 或者 writeFile 方式读写文件，为什么还需要流呢？

直接读写文件的方式，虽然简单，但是无法控制一些细节的操作：

- 比如从什么位置开始读、读到什么位置、一次性读取多少个字节；

- 读到某个位置后，暂停读取，某个时刻恢复读取等等；

- 或者这个文件非常大，比如一个视频文件，一次性全部读取并不合适；

**当有一些大文件操作时，我们就需要 Stream 像管道一样，一点一点的将数据流出，实现数据分段传输。**

在 Stream 中我们是不需要手动去创建自己的缓冲区，在 Node.js 的流中将会自动创建。

Node中很多对象是基于流实现的：

-  http 模块的 Request 和 Response 对象；

-  process.stdout 对象；

-  ......

![1_lhOvZiDrVbzF8_l8QX3ACw](https://cdn.nlark.com/yuque/0/2023/png/1614731/1683094103138-bbb52ae1-90f0-4ac4-8026-1646cb1220ca.png)

**所有的流都是 EventEmitter 的实例。**

Node.js 中有四种基本的流类型：

- Writable - 可写入数据的流（例如 fs.createWriteStream()）。
- Readable - 可读取数据的流（例如 fs.createReadStream()）。
- Duplex - 可读又可写的流（例如 net.Socket）。
- Transform - 在读写过程中可以修改或转换数据的 Duplex 流（例如 zlib.createDeflate() 数据压缩/解压）。

`Duplex streams`的例子包括

- `tcp sockets`
- `crypto streams`

第四种流不太常用

### 为什么应该使用 stream

因为一次性读取,操作大文件，内存和网络是吃不消的，因此要让数据流动起来，一点点的进行操作。

![Snipaste_2023-04-26_18-45-32](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682509977166-0e889342-4b53-4994-96da-a73d841a021b.png)

有个用户 Web 在线看视频的场景，假定我们通过 HTTP 请求返回给用户电影内容，那么代码可能写成这样

```javascript
const http = require('http');
const fs = require('fs');

http.createServer((req, res) => {
  fs.readFile(moviePath, (err, data) => {
     res.end(data);
  });
}).listen(8080);
```

这样的代码有两个明显的问题

1. 电影文件需要读完之后才能返回给客户，等待时间超长
2. 电影文件需要一次放入内存中，内存吃不消

使用 stream 可以把电影文件一点点的放入内存中，

然后一点点的返回给客户（利用了 HTTP 协议的 Transfer-Encoding: chunked 分段传输特性），

用户体验得到优化，同时对内存的开销明显下降

```javascript
const http = require('http');
const fs = require('fs');

http.createServer((req, res) => {
  fs.createReadStream(moviePath).pipe(res);
}).listen(8080);
```

除了上述好处，stream 还让代码优雅了很多，功能逻辑独立，拓展也比较简单。

比如需要对视频内容压缩，我们可以引入一个专门做此事的流，这个流不用关心其它部分做了什么，只要是接入管道中就可以了，下面只是例子，具体做法没有这么简单

```javascript
const http = require('http');
const fs = require('fs');
const oppressor = require(oppressor);

http.createServer((req, res) => {
  fs.createReadStream(moviePath)
     .pipe(oppressor)
     .pipe(res);
}).listen(8080);
```

可以看出来使用流后代码逻辑变得相对独立，可维护性也会有一定的改善

### stream 事件

除了从一个可读源流读取和往一个目标流中写入数据，pipe 方式自动管理一些事情。例如，处理错误，文件读取结束，或者一个流比另一个快或者慢的情形。

流可以被事件直接消费。这里有一个与 pipe 方法读取数据等价的使用事件消费流的代码：

```js
# readable.pipe(writable)
readable.on('data', (chunk) => {
  writable.write(chunk);
});
readable.on('end', () => {
  writable.end();
});
```

可读流和可写流使用的重要事件和函数列表：

![1_HGXpeiF5-hJrOk_8tT2jFA](https://cdn.nlark.com/yuque/0/2023/png/1614731/1683094809146-c4b11313-685e-431e-b140-d2220e6c516e.png)

在可读流上最重要的事件有：

- data 事件，当流传递给消费者一个数据块的时候会触发。
- end 事件，当在流中没有可以消费的数据的时候会触发。

在可写流上面最重要的事件有：

- drain 事件，当可写流可以接受更多的数据时的一个标志。
- finish 事件，当所有的数据都写入到底层系统中时会触发。

消费一个可读流，我们可以使用 pipe/unpipe 方法，或者 read/unshift/resume 方法。

消费一个可写流，我们可以将它作为 pipe/unpipe 的目标，

或者使用 write 方法写入，当我们完成的时候调用 end 方法。

### 可读流的暂停和流动模式

可读流有两个主要模式影响到我们消费它们的方式：

- 可以是暂停模式**（paused）** 
- 或者流动**（flowing）**模式

这些模式有时也被称为拉（pull）和推（push）的模式。

所有的可读流默认都是以暂停模式开始的，但是它们很容易切换到流动模式，并且在需要的时候退回到暂停模式。有时候，这种切换是自动发生的。

**当一个可读流在暂停模式，我们可以使用 read() 方法从流中按需读取数据，**

然而，对于一个在流动模式的可读流，数据持续流动，我们不得不监听事件去消费它。

处在流动模式，如果没有可获取的消费者去处理它，数据实际上可能会丢失，当有一个在流动模式的可读流，我们需要一个 data 事件处理函数。

**实际上，添加一个 data 事件处理函数就能将暂停状态的流切换到流动模式，移除 data 事件会将流切换回原来的暂停模式。**

这样做是为了兼容一些旧的流接口。

**为了手动切换两个流模式，可以使用 resume() 方法和 pause() 方法。**

![1_HI-mtispQ13qm8ib5yey3g](https://cdn.nlark.com/yuque/0/2023/png/1614731/1683095208863-8c2ec52e-9a48-4f8b-bb74-aaf4cf6cd77e.png)

**用 pipe 方法消费可读流时，我们不用担心那些模式，因为 pipe 会自动管理。**

### stream 有什么弊端

- 用 `rs.pipe(ws)` 的方式来写文件并不是把 rs 的内容 `append` 到 ws 后面，而是直接用 rs 的内容覆盖 ws 原有的内容
- 已结束/关闭的流不能重复使用，必须重新创建数据流
- `pipe` 方法返回的是目标数据流，如 `a.pipe(b)` 返回的是 b，因此监听事件的时候请注意你监听的对象是否正确
- 如果你要监听多个数据流，同时你又使用了 `pipe` 方法来串联数据流的话，你就要写成：

```js
data
    .on('end', function() {
        console.log('data end');
    })
    .pipe(a)
    .on('end', function() {
        console.log('a end');
    })
    .pipe(b)
    .on('end', function() {
        console.log('b end');
    });
```

### 例子

来个简单的例子，几行代码就实现了读取文件内容，并打印到控制台：

```js
const fs = require('fs');

fs.createReadStream('./sample.txt').pipe(process.stdout);
```

假设写程序某个功能需要读取某个配置文件 **config.json**，这时候简单分析一下

- 数据：config.json 的内容
- 方向：设备（物理磁盘文件） -> NodeJS 程序

应该使用 readable 流来做此事

```javascript
const fs = require('fs');
const FILEPATH = '...';

const rs = fs.createReadStream(FILEPATH);
```

通过 fs 模块提供的 createReadStream() 方法创建了一个可读的流，这时候 **config.json**的内容可以从设备流向程序。

示例并没有直接使用 Stream 模块，因为 fs 内部已经引用了 Stream 模块，并做了封装

读取到数据后可以对数据进行处理，比如需要写到某个路径 DEST ，这时候需要一个 writable 的流，让数据从程序流向设备

```javascript
const ws = fs.createWriteStream(DEST);
```

两种流都有了，也就是两个数据加工器，那么如何通过类似 Unix 的管道符 **|** 来连接流呢？

在 Node.js 中管道符号就是 pipe() 方法

```javascript
const fs = require('fs');

const FILEPATH = 'DEST_PATH_IN_YOUR_DISK';

const rs = fs.createReadStream(FILEPATH);
const ws = fs.createWriteStream(DEST);

rs.pipe(ws);
```

这样利用流实现了简单的文件复制功能，

有个值得注意地方：**数据必须是从上游 pipe 到下游，也就是从一个 readable 流生产数据从 pipe 管道到 writable 流消费数据。**

**通常建议使用 pipe 方法或者事件来消费流，但是不要混合使用它们。通常当你使用 pipe 方法时，不需要使用事件。**

![Snipaste_2023-04-26_18-43-02](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682513062499-b35bba2c-4a8b-459c-afc6-168d50971019.png)

![Snipaste_2023-04-26_18-42-44](https://cdn.nlark.com/yuque/0/2023/png/1614731/1682510121459-a69fbc8c-2624-4ed3-a18d-7a3fdce9aa7a.png)

**加工一下数据**

上面提到了 readable 和 writable 的流称之为加工器，其实并不太恰当，因为过程中并没有加工什么，只是读取数据，然后存储数据

如果有个需求，把本地一个 package.json 文件中的所有字母都改为小写，并保存到同目录下的 package-lower.json 文件下

这时候就需要用到双向的流了，假定有一个专门处理字符转小写的流 `lowercase` ，那么代码写出来大概是这样的

```javascript
const fs = require('fs');
const rs = fs.createReadStream('./package.json');
const ws = fs.createWriteStream('./package-lower.json');
rs.pipe(lowercase).pipe(ws);
```

到这里就和清楚为什么称 pipe() 连接的流为加工器了，根据上面说的，必须从一个 readable 流 pipe 到 writable 流：

- rs -> lowercase：lowercase 在下游，所以 lower 需要是个 writable 流
- lowercase -> ws：相对而言，lowercase 又在上游，所以 lower 需要是个 readable 流

因此能够满足需求的 lowercase 必须是双向的流，具体使用 duplex 。

当然如果需求还有额外一些处理动作，比如字母还需要转成 ASCII 码，假定有一个双工流 ascii，那么代码简单改写

```js
rs.pipe(lowercase).pipe(acsii).pipe(ws);
```

这样就完成了 package.json 文件字母转小写后处理成 acsii 码的需求

### stream 流转过程 pipe

![640](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1682512796752-d063090d-d871-4b96-87c6-9405a74538e0.jpeg)

**soucre**

`stream`的常见来源方式有三种：

1. 从控制台输入
2. `http`请求中的`request`
3. 读取文件

**连接水桶的管道-pipe**

从水桶管道流转图中可以看到，

在`source`和`dest`之间有一个连接的管道`pipe`,

它的基本语法是`source.pipe(dest)`，`source`和`dest`就是通过pipe连接，让数据从`source`流向了`dest`。

**pipe 方法将一个可写流附到可读流上, 同时将可写流切换到流模式, 并把所有数据推给可写流。**

在 pipe 传递数据的过程中, objectMode 是传递引用, 非 objectMode 则是拷贝一份数据传递下去.

pipe 方法最主要的目的就是将数据的流动缓冲到一个可接受的水平, 不让不同速度的数据源之间的差异导致内存被占满

pipe 这个方法两个参数：

- destination：是一个可写流对象，也就是一个数据写入的目标对象，例如，上面我们创建的 writeable 就是一个可写流对象
- options：
  - end：读取结束时终止写入流，默认值是 true

```javascript
readable.pipe(destination[, options])
```

**stream 到哪里去-dest**

stream的常见输出方式有三种：

1. 输出控制台
2. `http`请求中的`response`
3. 写入文件

### stream 应用场景

stream的应用场景主要就是处理 IO 操作，而 http 请求和文件操作都属于 IO 操作。

这里再提一下 stream 的本质——由于一次性 IO 操作过大，硬件开销太多，影响软件运行效率，

因此将 IO 分批分段进行操作，让数据像水管一样流动起来，直到流动完成，也就是操作完成。

#### post 与 get 使用 stream 总结

request 和 reponse 一样，都是 stream 对象，可以使用 stream 的特性，二者的区别在于，我们再看一下水桶管道流转图，

![640](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1682513193580-63a32cf8-1c67-4c08-911a-4b597ea4d9ba.jpeg)

图片request是source类型，是图中的源头，而response是dest类型，是图中的目的地。

**get 请求中应用 stream**

使用node.js实现一个http请求，读取data.txt文件，创建一个服务，监听8000端口，读取文件后返回给客户端

```js
const server = http.createServer(function (req, res) {
    const method = req.method; // 获取请求方法
    if (method === 'GET') { // get 请求
        const fileName = path.resolve(__dirname, 'data.txt');
        let stream = fs.createReadStream(fileName);
        stream.pipe(res); // 将 res 作为 stream 的 dest
    }
});
server.listen(8000);
```

**post 中使用 stream**

一个通过 post 请求微信小程序的地址生成二维码的需求。

```js
/*
* 微信生成二维码接口
* params src 微信url / 其他图片请求链接
* params localFilePath: 本地路径
* params data: 微信请求参数
* */
const downloadFile = async (src, localFilePath, data)=> {
    try {
        const ws = fs.createWriteStream(localFilePath);
        returnnewPromise((resolve, reject) => {
            ws.on('finish', () => {
                resolve(localFilePath);
            });
            if (data) {
                request({
                    method: 'POST',
                    uri: src,
                    json: true,
                    body: data
                }).pipe(ws);
            } else {
                request(src).pipe(ws);
            }
        });
    } catch (e) {
        logger.error('wxdownloadFile error: ',e);
        throw e;
    }
}
```

看这段使用了 stream 的代码，为本地文件对应的路径创建一个 stream 对象，

然后直接`.pipe(ws)`,将post请求的数据流转到这个本地文件中，

这种 stream 的应用在 node 后端开发过程中还是比较常用的。

#### 在文件操作中使用 stream

一个文件拷贝的例子

```js
const fs = require('fs')
const path = require('path')

// 两个文件名
const fileName1 = path.resolve(__dirname, 'data.txt')
const fileName2 = path.resolve(__dirname, 'data-bak.txt')
// 读取文件的 stream 对象
const readStream = fs.createReadStream(fileName1)
// 写入文件的 stream 对象
const writeStream = fs.createWriteStream(fileName2)
// 通过 pipe 执行拷贝，数据流转
readStream.pipe(writeStream)
// 数据读取完成监听，即拷贝完成
readStream.on('end', function () {
    console.log('拷贝完成')
})
```

看了这段代码，发现是不是拷贝好像很简单，

创建一个可读数据流`readStream`，一个可写数据流`writeStream `,然后直接通过`pipe`管道把数据流转过去。

这种使用 stream 的拷贝相比存文件的读写实现拷贝，性能要增加很多。

### 缓冲区

Readable 和 Writable 流都会将数据储存在内部的缓冲区中。

缓冲区可以分别通过 `writable._writableState.getBuffer() `和 `readable._readableState.buffer` 来访问. 缓冲区的大小, 

由构造 stream 时候的 `highWaterMark` 标志指定可容纳的 byte 大小, 

对于 `objectMode `的 stream, 该标志表示可以容纳的对象个数.

### 可读流

当一个可读实例调用 `stream.push()` 方法的时候, 数据将会被推入缓冲区.。

如果数据没有被消费, 即调用 `stream.read()` 方法读取的话, 那么数据会一直留在缓冲队列中。

当缓冲区中的数据到达 `highWaterMark` 指定的阈值, 可读流将停止从底层汲取数据, 直到当前缓冲的报备成功消耗为止。

#### 自定义可读流

如果希望自己以某种特定的方式生产数据，交给程序消费，那么该如何开始呢？

简单两步即可

1. 继承 stream 模块的 Readable 类
2. 重写 _read 方法，调用 this.push 将生产的数据放入待读取队列

Readable 类已经把可读流要做的大部分工作完成，只需要继承它，然后把生产数据的方式写在 _read 方法里就可以实现一个自定义的可读流。

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1682512187686-fa804ee7-049d-42ac-996d-4f251b10db6d.png" alt="Snipaste_2023-04-26_19-51-01" style="zoom: 67%;" />

举个例子：实现一个每 100 毫秒生产一个随机数的流（没什么用处）

```javascript
const Readable = require('stream').Readable;
class RandomNumberStream extends Readable {
    constructor(max) {
        super()
    }
    _read() {
        const ctx = this;
        setTimeout(() => {
            const randomNumber = parseInt(Math.random() * 10000);
            // 只能 push 字符串或 Buffer，为了方便显示打一个回车
            ctx.push(`${randomNumber}\n`);
        }, 100);
    }
}
module.exports = RandomNumberStream;
```

类继承部分代码很简单，主要看一下 _read 方法的实现，有几个值得注意的地方

1. Readable 类中默认有 _read 方法的实现，不过什么都没有做，我们做的是覆盖重写

2. _read 方法有一个参数 size，用来向 read 方法指定应该读取多少数据返回，不过只是一个参考数据

   很多实现忽略此参数，我们这里也忽略了，后面会详细提到

3. 通过 this.push 向缓冲区推送数据，缓冲区概念后面会提到，暂时理解为挤到了水管中可消费了

4. push 的内容只能是字符串或者 Buffer，不能是数字

5. push 方法有第二个参数 encoding，用于第一个参数是字符串时指定 encoding

执行一下看看效果

```javascript
const RandomNumberStream = require('./RandomNumberStream');
const rns = new RandomNumberStream();
rns.pipe(process.stdout);
```

这样可以看到数字源源不断的显示到了控制台上，实现了一个产生随机数的可读流，还有几个小问题待解决

##### 如何停下来

每隔 100 毫秒向缓冲区推送一个数字，那么就像读取一个本地文件总有读完的时候，如何停下来标识数据读取完毕？

向缓冲区 push 一个 null 就可以，修改一下代码，允许消费者定义需要多少个随机数字：

```javascript
const Readable = require('stream').Readable;
class RandomNumberStream extends Readable {
    constructor(max) {
        super()
        this.max = max;
    }
    _read() {
        const ctx = this;
        setTimeout(() => {
            if (ctx.max) {
                const randomNumber = parseInt(Math.random() * 10000);
                // 只能 push 字符串或 Buffer，为了方便显示打一个回车
                ctx.push(`${randomNumber}\n`);
                ctx.max -= 1;
            } else {
                ctx.push(null);
            }
        }, 100);
    }
}
module.exports = RandomNumberStream;
```

代码中使用了一个 max 的标识，允许消费者指定需要的字符数，在实例化的时候指定即可

```javascript
const RandomNumberStream = require('./RandomNumberStream');
const rns = new RandomNumberStream(5);
rns.pipe(process.stdout);
```

这样可以看到控制台只打印了 5 个字符

##### 为什么是 setTimeout 而不是 setInterval

细心的同学可能注意到，每隔 100 毫秒生产一个随机数并不是调用的 setInterval，而是使用的 setTimeout，

为什么仅仅是延时了一下并没有重复生产，结果却是正确的呢？

这就需要了解流的两种工作方式

1. 流动模式：数据由底层系统读出，并尽可能快地提供给应用程序
2. 暂停模式：必须显示地调用 read() 方法来读取若干数据块

**流在默认状态下是处于暂停模式的，也就是需要程序显式的调用 read() 方法，**

**上面例子中并没有调用就可以得到数据，因为流通过 pipe() 方法切换成了流动模式，这样 _read() 方法会自动被反复调用，**

直到数据读取完毕，所以每次 _read() 方法里面只需要读取一次数据即可

##### 流动模式和暂停模式切换

流从默认的暂停模式切换到流动模式可以使用以下几种方式：

1. 通过添加 data 事件监听器来启动数据监听
2. 调用 resume() 方法启动数据流
3. 调用 pipe() 方法将数据转接到另一个可写流

从流动模式切换为暂停模式有两种方法：

1. 在流没有 pipe() 时，调用 pause() 方法可以将流暂停
2. pipe() 时移除所有 data 事件的监听，再调用 unpipe() 方法

##### data 事件

说明：`stream`对象可以监听`"data"`,`"end"`,`"opne"`,`"close"`,`"error"`等事件。

使用了 pipe() 方法后数据就从可读流进入了可写流，但对用户好像是个黑盒，数据究竟是怎么流向的呢？

切换流动模式和暂停模式的时候有两个重要的名词

1. 流动模式对应的 data 事件
2. 暂停模式对应的 read() 方法

这两个机制是程序能够驱动数据流动的原因，

先来看一下流动模式 data 事件，一旦监听了可读流的 data 事件，流就进入了流动模式，可以改写一下上面调用流的代码

```javascript
const RandomNumberStream = require('./RandomNumberStream');
const rns = new RandomNumberStream(5);
rns.on('data', chunk => {
  console.log(chunk);
});
```

这样可以看到控制台打印出了类似下面的结果

```plain
<Buffer 39 35 37 0a>
<Buffer 31 30 35 37 0a>
<Buffer 38 35 31 30 0a>
<Buffer 33 30 35 35 0a>
<Buffer 34 36 34 32 0a>
```

当可读流生产出可供消费的数据后就会触发 data 事件，data 事件监听器绑定后，数据会被尽可能地传递。

data 事件的监听器可以在第一个参数收到可读流传递过来的 Buffer 数据，这也就是控制台打印的 chunk，

如果想显示为数字，可以调用 Buffer 的 toString() 方法。

当数据处理完成后还会触发一个 end 事件，

因为流的处理不是同步调用，所以如果希望完事后做一些事情就需要监听这个事件，在代码最后追加一句：

```javascript
rns.on('end', () => {
  console.log('done');
});
```

这样可以在数据接收完了显示 `done` ，当然数据处理过程中出现了错误会触发 error 事件，可以监听做异常处理：

```javascript
rns.on('error', (err) => {
  console.log(err);
});
```

##### read(size)

流在暂停模式下需要程序显式调用 read() 方法才能得到数据，read() 方法会从内部缓冲区中拉取并返回若干数据，当没有更多可用数据时，会返回 null

使用 read() 方法读取数据时，如果传入了 size 参数，那么它会返回指定字节的数据；

当指定的 size 字节不可用时，则返回 null 。

如果没有指定 size 参数，那么会返回内部缓冲区中的所有数据。

现在有一个矛盾，在流动模式下流生产出了数据，然后触发 data 事件通知给程序，这样很方便。

在暂停模式下需要程序去读取，那么就有一种可能是读取的时候还没生产好，如果使用轮询的方式未免效率有些低。

Node.JS 提供了一个 readable 的事件，事件在可读流准备好数据的时候触发，也就是先监听这个事件，收到通知有数据了再去读取就好了：

```javascript
const rns = new RandomNumberStream(5);
rns.on('readable', () => {
  let chunk;
  while((chunk = rns.read()) !== null){
    console.log(chunk);
  }
});
```

这样可以读取到数据，值得注意的一点是并不是每次调用 read() 方法都可以返回数据，

前面提到了如果可用的数据没有达到 size 那么返回 null，所以在程序中加了个判断

##### 数据会不会漏掉

```javascript
const stream = fs.createReadStream('/dev/input/event0');
stream.on('readable', callback);
```

在流动模式会不会有这样的问题：可读流在创建好的时候就生产数据了，

如果在绑定 readable 事件之前就生产了某些数据，触发了 readable 事件，在极端情况下会造成数据丢失吗？

事实并不会

可读流是生产数据用来供程序消费的流。常见的数据生产方式有读取磁盘文件、读取网络请求内容等。

- start：文件读取开始的位置；
- end：文件读取结束的位置；
- highWaterMark：一次性读取字节的长度，默认是 64 kb；  

```js
// 传统的方式
// fs.readFile('./foo.txt', (err, data) => {
//   console.log(data);
// });

// 流的方式读取
const reader = fs.createReadStream("./foo.txt", {
  start: 3,
  end: 10,
  highWaterMark: 2
});

// 数据读取的过程
reader.on("data", (data) => {
  console.log(data);

  reader.pause();

  setTimeout(() => {
    reader.resume();
  }, 1000);
});

reader.on('open', () => {
  console.log("文件被打开");
})

reader.on('close', () => {
  console.log("文件被关闭");
})
```

### 可写流

在一个在可写实例上不停地调用 writable.write(chunk) 的时候数据会被写入可写流的缓冲区。

如果当前缓冲区的缓冲的数据量低于 highWaterMark 设定的值, 调用 writable.write() 方法会返回 true (表示数据已经写入缓冲区), 

否则当缓冲的数据量达到了阈值, 数据无法写入缓冲区 write 方法会返回 false, 

直到 drain 事件触发之后才能继续调用 write 写入。

```js
// 传统的写入方式
// fs.writeFile("./bar.txt", "Hello Stream", {flag: "a"}, (err) => {
//   console.log(err);
// });

// Stream的写入方式
const writer = fs.createWriteStream('./bar.txt', {
  flags: "r+",
  start: 4
});

writer.write("你好啊", (err) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log("写入成功");
});

writer.write("李银河", (err) => {
  console.log("第二次写入");
})

// writer.close();
// write("Hello World");
// close();
writer.end("Hello World");

writer.on('close', () => {
  console.log("文件被关闭");
})
```









**默认情况下我们是不需要手动调用写入流的 end 方法关闭的**。

现在我们改一下，**设置 end 为 false 写入的目标流将会一直处于打开状态，** 此时就需要监听可读流的 end 事件，结束之后手动调用可写流的 end 事件。

```js
// readable.pipe(writeable);

readable.pipe(writeable, {
  end: false,
});
readable.on('end', function() {
  writeable.end('结束');
});
```

**还需要注意一点如果可读流期间发生什么错误，则写入的目标流将不会关闭，**

例如：process.stderr 和 process.stdout 可写流在 Nodejs 进程退出前将永远不会关闭，

所以需要监听错误事件，手动关闭可写流，防止内存泄漏。

Linux 下一切皆文件，为了测试，在创建可读流时，你可以不创建 test1.txt 文件，

让可读流自动触发 error 事件并且将 writeable 的 close 方法注释掉，通过 linux 命令 ls -l /proc/${pid}/fd 查看 error 和非 error 前后的文件句柄变化。

```javascript
readable.on('error', function(err) {
  console.log('error', err);
  // writeable.close();
});

console.log(process.pid); // 打印进程 ID
setInterval(function(){}, 5000) // 让程序不中断，进程不退出
```

触发 error 错误下 test2.txt 这个文件 fd 将会一直打开，除非进程退出，

所以重要的事情再说一遍，**一定要做好错误监听手动关闭每个写入流，以防止 “内存泄漏”。**



## child_process

4个异步方法：exec、execFile、fork、spawn

- Node
  - fork：想将一个 Node 进程作为一个独立的进程来运行的时候使用，使计算处理和文件描述器脱离 Node 主进程
- 非 Node
  - spawn：处理一些会有很多子进程 I/O 时、进程会有大量输出时使用
  - execFile：只需执行一个外部程序的时候使用，执行速度快，处理用户输入相对安全
  - exec：想直接访问线程的 shell 命令时使用，一定要注意用户输入

3个同步方法：execSync、execFileSync、spawnSync

```js
const cp = require('child_process');

// spawn
cp.spawn('node', ['./dir/test1.js'],
  { stdio: 'inherit' }
);
// exec
cp.exec('node ./dir/test1.js', (err, stdout, stderr) => {
  console.log(stdout);
});
// execFile
cp.execFile('node', ['./dir/test1.js'],(err, stdout, stderr) => {
  console.log(stdout);
});
// fork
cp.fork('./dir/test1.js',
  { silent: false }
);

// ./dir/test1.js
console.log('test1 输出...');
```

差异点：

- spawn 与 exec、execFile 不同的是，后两者创建时可以指定 timeout 属性设置超时时间，一旦创建的进程运行超过设定的时间将会被杀死；
- exec 与 execFile 不同的是，exec 适合执行已有的命令，execFile 适合执行文件；
- exec、execFile、fork 都是 spawn 的延伸应用，底层都是通过 spawn 实现的；

差异列表如下：

| 类型     | 回调/异常 | 进程类型 | 执行类型        | 可设置超时 |
| -------- | --------- | -------- | --------------- | ---------- |
| spawn    | 不支持    | 任意     | 命令            | 不支持     |
| exec     | 支持      | 任意     | 命令            | 支持       |
| execFile | 支持      | 任意     | 可执行文件      | 支持       |
| fork     | 不支持    | Node     | JavaScript 文件 | 不支持     |

`child_process.spawn()`：

适用于耗时任务。返回大量数据，例如图像处理，二进制数据处理，npm install 时不断出现的大量日志。

```js
const spawn = require('child_process').spawn;
const ls = spawn('ls', ['-al']);

// 输出相关的数据
ls.stdout.on('data', function(data){
    console.log('data from child: ' + data);
});

// 错误的输出
ls.stderr.on('data', function(data){
    console.log('error from child: ' + data);
});

// 子进程结束时输出
ls.on('close', function(code){
    console.log('child exists with code: ' + code);
});
```

声明 stdio

父子进程共用一个输出管道；

```javascript
// 2、声明 stdio
var ls = spawn('ls', ['-al'], {
  stdio: 'inherit'
});

ls.on('close', function(code){
  console.log('child exists with code: ' + code);
});
```

错误场景

```javascript
// 3、错误处理
// 3.1、场景1: 命令本身不存在，创建子进程报错
const child = spawn('bad_command');

child.on('error', (err) => {
  console.log('Failed to start child process 1: ', err);
});

// 3.2、场景2: 命令存在，但运行过程报错
const child2 = spawn('ls', ['nonexistFile']);

child2.stderr.on('data', function(data){
    console.log('Error msg from process 2: ' + data);
});

child2.on('error', (err) => {
  console.log('Failed to start child process 2: ', err);
});
```

`child_process.execFile()`：

执行可执行文件，类似 `child_process.exec()`，区别是不能通过 shell 来执行，不支持像 I/O 重定向和文件查找这样的行为。

也可以写个例如 .sh 的文件，然后执行它即可

执行 node 文件

```js
const { execFile } = require('child_process');

// 1、执行命令
execFile('node', ['./dir/test1.js'], (error, stdout, stderr) => {
  if (error) {
    console.error('error:', error);
    return;
  }
  console.log('stdout: ' + stdout); 
  console.log('stderr: ' + stderr);
})
```

执行 shell 脚本文件

```js
const { execFile } = require('child_process');

// 2、执行 shell 脚本
// 在 shell 脚本中可以访问到 process.env 的属性 
process.env.DIRNAME = __dirname;
execFile(`${__dirname}/dir/test2.sh`, (error, stdout, stderr) => {
  if (error) {
    console.error('error:', error);
    return;
  }
  console.log('stdout: ' + stdout); // stdout: 执行 test2.sh  test1 输出...
  console.log('stderr: ' + stderr);
})

// ./dir/test2.sh

#! /bin/bash
echo '执行 test2.sh'
node $DIRNAME/dir/test1.js


// ./dir/test1.js
console.log('test1 输出...');
```

`child_process.exec()`：

执行 shell 命令

适用于开销小的任务，小量数据，maxBuffer 默认值为 200 * 1024 ，如果超出的话，程序会崩溃子进程会被杀死，数据量过大可采用 spawn。

```js
const { exec } = require('child_process');

exec('ls', (error, stdout, stderr) => {
  if (error) {
    console.error('error:', error);
    return;
  }
  console.log('stdout: ' + stdout);
  console.log('stderr: ' + stderr);
})


exec('ls', {cwd: __dirname + '/dir'}, (error, stdout, stderr) => {
  if (error) {
    console.error('error:', error);
    return;
  }
  console.log('stdout: ' + stdout);
  console.log('stderr: ' + stderr);
})
```

子进程输出/错误监听

```js
const child = exec('node ./dir/test1.js')

child.stdout.on('data', data => {
  console.log('stdout 输出:', data);
})
child.stderr.on('data', err => {
  console.log('error 输出:', err);
})
```

`child_process.fork()`： 

衍生新的进程，进程之间是相互独立的，每个进程都有自己的 V8 实例、内存，系统资源是有限的，

不建议衍生太多的子进程出来，通常根据系统CPU 核心数设置。

Node.js 里通过 node app.js 开启一个服务进程，

多进程就是进程的复制（fork），fork 出来的每个进程都拥有自己的独立空间地址、数据栈，

一个进程无法访问另外一个进程里定义的变量、数据结构，只有建立了 IPC 通信，进程之间才可数据共享。

在单核 CPU 系统之上我们采用 `单进程 + 单线程` 的模式来开发。

在多核 CPU 系统之上，可以用过 child_process.fork 开启多个进程（Node.js 在 v0.8 版本之后新增了Cluster 来实现多进程架构） ，

即 `多进程 + 单线程` 模式。注意：开启多进程不是为了解决高并发，

主要是解决了单进程模式下 Node.js CPU 利用率不足的情况，充分利用多核 CPU 的性能。

silent

```js
const { fork } = require('child_process');

// 1、默认 silent 为 false，子进程会输出 output from the child3
fork('./dir/child3.js', {
  silent: false
});

// 2、设置 silent 为 true，则子进程不会输出
fork('./dir/child3.js', {
  silent: true
});

// 3、通过 stdout 属性，可以获取到子进程输出的内容
const child3 = fork('./dir/child3.js', {
  silent: true
});

child3.stdout.setEncoding('utf8');
child3.stdout.on('data', function (data) {
  console.log('stdout 中输出：');
  console.log(data);
});
```

事件

```js
// 例子
const { fork } = require('child_process');

const child = fork('./dir/test5.js')

child.on('close', (code, signal) => {
  console.log('close 事件：', code, signal);
})

child.on('disconnect', () => {
  console.log('disconnect 事件...');
})

child.on('error', (code, signal) => {
  console.log('error 事件：', code, signal);
})

child.on('exit', (code, signal) => {
  console.log('exit 事件：', code, signal);
})

child.on('message', (val) => {
  console.log('message 事件：', val);
})

// ./dir/test5.js
console.log('event_test 输出...');
process.send('子进程发送给父进程的消息...')
```

进程间通信

```js
// 父进程
const child3 = fork('./dir/child3_1.js');

child3.on('message', (m)=>{
  console.log('message from child: ' + JSON.stringify(m));
});

child3.send({from: 'parent'});

// 子进程
process.on('message', function(m){
  console.log('message from parent: ' + JSON.stringify(m));
});

process.send({from: 'child'});
```

## http 

### 创建服务器的两种方式

创建服务器对象，我们是通过 createServer 来完成的

- http.createServer会返回服务器的对象；

- 底层其实使用直接 new Server 对象

创建 server 时会传入一个回调函数，这个回调函数在被调用时会传入两个参数：

- req：request 请求对象，包含请求相关的信息；
- res：response 响应对象，包含要发送给客户端的信息

```js
const http = require('http');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  res.end("Hello Server");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

```js
const http = require('http');

// 创建server的两种方式
const server1 = http.createServer((req, res) => {
  res.end("Server1");
});

// 2.监听方法的使用
server1.listen(8000, () => {
  console.log("server1启动成功~");
  // console.log(server1.address().port);
});

// const server2 = http.createServer((req, res) => {
//   res.end("Server2");
// });

// const server2 = new http.Server((req, res) => {
//   res.end("Server2");
// });

// server2.listen(8001, () => {
//   console.log("server2启动成功~");
// });
```

server 通过 listen 方法来开启服务器，并且在某一个主机和端口上监听网络请求：

也就是当我们通过 ip:port的方式发送到我们监听的Web服务器上时；我们就可以对其进行相关的处理；

listen 函数有三个参数：

1. 端口port: 可以不传, 系统会默认分配端, 项目中可以写入到环境变量中；

   

2. 主机host: 通常可以传入 localhost 、ip 地址 127.0.0.1、或者 ip 地址 0.0.0.0，默认是 0.0.0.0 ；

- localhost：本质上是一个域名，通常情况下会被解析成127.0.0.1；


- 127.0.0.1：回环地址（Loop Back Address），表达的意思其实是我们主机自己发出去的包，直接被自己接收；

​       正常的数据库包经过 应用层 - 传输层 - 网络层 - 数据链路层 - 物理层 ；

​       而回环地址，是在网络层直接就被获取到了，是不会经常数据链路层和物理层的；

​       比如我们监听 127.0.0.1时，在同一个网段下的主机中，通过ip地址是不能访问的；

- 0.0.0.0：监听IPV4上所有的地址，再根据端口找到不同的应用程序；

  比如我们监听 0.0.0.0时，在同一个网段下的主机中，通过 ip 地址是可以访问的；

3. 回调函数：服务器启动成功时的回调函数；

### request 对象

向服务器发送请求时，我们会携带很多信息，比如：

- 本次请求的 URL，服务器需要根据不同的URL进行不同的处理；


- 本次请求的请求方式，比如GET、POST请求传入的参数和处理的方式是不同的；


- 本次请求的 headers 中也会携带一些信息，比如客户端信息、接受数据的格式、支持的编码格式等；
- 等等


```js
const http = require('http');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  // request对象中封装了客户端给我们服务器传递过来的所有信息
  console.log(req.url);
  console.log(req.method);
  console.log(req.headers);

  res.end("Hello Server");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

### URL 的处理

```
"  https:   //    user   :   pass   @ sub.example.com : 8080   /p/a/t/h  ?  query=string   #hash "
│          │  │          │          │    hostname     │ port │          │                │       │
│          │  │          │          ├─────────────────┴──────┤          │                │       │
│ protocol │  │ username │ password │          host          │          │                │       │
├──────────┴──┼──────────┴──────────┼────────────────────────┤          │                │       │
│   origin    │                     │         origin         │ pathname │     search     │ hash  │
├─────────────┴─────────────────────┴────────────────────────┴──────────┴────────────────┴───────┤
│                                              href                                              │
```

客户端在发送请求时，会请求不同的数据，那么会传入不同的请求地址：
- 比如 http://localhost:8000/login；
- 比如 http://localhost:8000/products;
- 服务器端需要根据不同的请求地址，作出不同的响应：

URL的解析

如果用户发送的地址中还携带一些额外的参数呢？

- 比如： http://localhost:8000/login?name=why&password=123 这个时候，url 的值是 /login?name=why&password=123；

url.parse() 方法接受网址字符串，解析并返回网址对象。

url.parse() 使用一种宽松的、非标准的算法来解析 URL 字符串。 它很容易出现安全问题，例如 主机名欺骗 和用户名和密码的不正确处理，

>建议改用 WHATWG URL API （即 new URL）。在较早的版本使用 Node.js 内置模块 querystring 来操作 url querystring，简单场景可以不再使用。

Node.js 中的 URL 类和浏览器 URL API 完全兼容，可以通过 `require('url').URL` 使用，也可以使用全局变量 URL 

new URL(input[, base])：实例化一个 URL 对象

1. input：要解析的绝对或相对的 URL。如果 `input` 是相对路径，则需要 `base`；如果 `input` 是绝对路径，则忽略 `base`
2. base：如果 `input` 不是绝对路径，则为要解析的基础地址

querystring 模块是 Node.js 中的工具模块之一，用于处理 URL 中的查询字符串，即：querystring 部分。

查询字符串指：

URL 字符串中，从问号"?"(不包括?)开始到锚点"#"或者到 URL 字符串的结束（存在#，则到＃结束，不存在则到 URL 字符串结束）的部分叫做查询字符串。

querystring 模块可将 URL 查询字符串解析为对象，或将对象序列化为查询字符串。

旧写法：

```js
const http = require('http');
const url = require('url');
const qs = require('querystring');

// 创建一个web服务器
const server = http.createServer((req, res) => {

  // 最基本的使用方式
  // if (req.url === '/login') {
  //   res.end("欢迎回来~");
  // } else if (req.url === '/users') {
  //   res.end("用户列表~");
  // } else {
  //   res.end("错误请求, 检查~");
  // }

  // req.url 为 /login?username=why&password=123
  const { pathname, query } = url.parse(req.url);
  if (pathname === '/login') {
    console.log(query);
    console.log(qs.parse(query));
    const { username, password } = qs.parse(query);
    console.log(username, password);
    res.end("请求结果~");
  }

  // console.log(req.url);
  // res.end("请求结果~");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

新写法：

```js
const http = require('http');
const { URL } = require("url");
// 创建一个web服务器
const server = http.createServer((req, res) => {
  const { pathname, searchParams } = new URL(req.url, 'http://localhost:8888');;
  if (pathname === '/login') {
    const [username, password] = searchParams.values();
    console.log(username, password);
    res.end("请求结果~");
  }
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

### method 的处理

在Restful规范（设计风格）中，我们对于数据的增删改查应该通过不同的请求方式：

- GET：查询数据；

- POST：新建数据；

- PATCH：更新数据；

- DELETE：删除数据；

所以，我们可以通过判断不同的请求方式进行不同的处理。

比如创建一个用户：

- 请求接口为 /users；

- 请求方式为 POST请求；

- 携带数据 username 和 password；

程序中如何进行判断以及获取对应的数据呢？

- 这里我们需要判断接口是 /users，并且请求方式是POST方法去获取传入的数据；
- 获取这种body携带的数据，我们需要通过监听req的 data事件来获取;
- 将JSON字符串格式转成对象类型，通过JSON.parse方法即可

```js
const http = require('http');
const url = require('url');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  const { pathname } = url.parse(req.url);
  if (pathname === '/login') {
    if (req.method === 'POST') {
      // 拿到body中的数据
      req.setEncoding('utf-8');
      req.on('data', (data) => {
        const {username, password} = JSON.parse(data);
        console.log(username, password);
      });

      res.end("Hello World");
    }
  }
});

server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

### headers 的处理

在 request 对象的 headers 中也包含很多有用的信息，客户端会默认传递过来一些信息

```js
const http = require('http');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  console.log(req.headers);

  req.on('data', (data) => {
    
  })

  res.end("Hello Server");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

![Image_041](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1683435018812-d73c55b3-44b9-44d0-ad1b-dece5599d338.jpeg)

content-type 是这次请求携带的数据的类型：
-  application/json表示是一个json类型；
-  text/plain表示是文本类型；
- application/xml表示是xml类型；
- multipart/form-data表示是上传文件；

content-length：文件的大小和长度

keep-alive：

http 是基于 TCP 协议的，但是通常在进行一次请求和响应结束后会立刻中断；

- 在http1.0中，如果想要继续保持连接：
-  浏览器需要在请求头中添加 connection: keep-alive；
-  服务器需要在响应头中添加 connection:keey-alive；
-  当客户端再次放请求时，就会使用同一个连接，直接一方中断连接；
-  在http1.1中，所有连接默认是 connection: keep-alive的；
-  不同的Web服务器会有不同的保持 keep-alive的时间；
-  Node中默认是5s；

accept-encoding：告知服务器，客户端支持的文件压缩格式，比如js文件可以使用 gzip 编码，对应 .gz文件；

accept：告知服务器，客户端可接受文件的格式类型；

user-agent：客户端相关的信息；

### 返回响应结果

如果我们希望给客户端响应的结果数据，可以通过两种方式：

- write 方法：这种方式是直接写出数据，但是并没有关闭流；
- end 方法：这种方式是写出最后的数据，并且写出后会关闭流；

如果我们没有调用 end 和 close，客户端将会一直等待结果，所以客户端在发送网络请求时，都会设置超时时间

```js
const http = require('http');
// 创建一个web服务器
const server = http.createServer((req, res) => {
  // 响应结果
  res.write("响应结果一");
  res.end("Hello World");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

### 返回响应状态码

Http 状态码（Http Status Code）是用来表示 Http 响应状态的数字代码：

- Http状态码非常多，可以根据不同的情况，给客户端返回不同的状态码；
- 常见的状态码是下面这些（后续项目中，也会用到其中的状态码）；

![Image_052](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1683435556936-c52c9037-75be-4b12-b8e1-25b683a20551.jpeg)

```js
const http = require('http');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  // 设置状态码
  // 方式一: 直接给属性赋值
  // res.statusCode = 400;
  // 方式二: 和Head一起设置
  res.writeHead(503)

  // 响应结果
  res.write("响应结果一");
  res.end("Hello World");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

### 返回响应头

Header 设置 Content-Type 有什么作用呢？

默认客户端接收到的是字符串，客户端会按照自己默认的方式进行处理；

这里设置客户端以 html 方式处理内容

```js
const http = require('http');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  // 设置响应的header
  // 设置方式一:
  // res.setHeader("Content-Type", "text/plain;charset=utf8");
  res.writeHead(200, {
    "Content-Type": "text/html;charset=utf8"
  });

  // 响应结果
  res.end("<h2>Hello Server</h2>");
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```

![Image_059](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1683435718196-0d61a153-3567-4caa-ac4d-d3728704490a.jpeg)

### 发送网络请求

```js
const http = require('http');

// http 发送get请求
// http.get('http://localhost:8888', (res) => {
//   res.on('data', (data) => {
//     console.log(data.toString());
//   });

//   res.on('end', () => {
//     console.log("获取到了所有的结果");
//   })
// })

// http 发送post请求
const req = http.request({
  method: 'POST',
  hostname: 'localhost',
  port: 8888
}, (res) => {
  res.on('data', (data) => {
    console.log(data.toString());
  });

  res.on('end', () => {
    console.log("获取到了所有的结果");
  })
});

req.end();
```

axios 库可以在浏览器中使用，也可以在 Node 中使用：

- 在浏览器中，axios 使用的是封装 xhr ；

- 在 Node 中，使用的是 http 内置模块；

### 文件上传错误做法

无法打开图片，因为图片信息里包含了 request.body 的其它东西，不只有图片内容

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1683436395942-fa8023ba-10cd-44e9-9a5c-a3f3fd5cfbf1.png" alt="Snipaste_2023-05-07_13-12-54" style="zoom:50%;" />

![Snipaste_2023-05-07_13-11-52](https://cdn.nlark.com/yuque/0/2023/png/1614731/1683436328666-69f26f89-0113-44fe-9812-c986549d3dd5.png)

```js
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
  if (req.url === '/upload') {
    if (req.method === 'POST') {
      const fileWriter = fs.createWriteStream('./foo.png', {flags: 'a+'});
      // req.pipe(fileWriter);

      req.on('data', (data) => {
        // console.log(data);
        // fileWriter.write(data);
      });

      req.on('end', () => {
        console.log("文件上传成功~");
        res.end("文件上传成功~");
      })
    }
  }
});

server.listen(8000, () => {
  console.log("文件上传服务器开启成功~");
})

```

### 文件上传正确做法

```js
const http = require('http');
const fs = require('fs');
const qs = require('querystring');

const server = http.createServer((req, res) => {
  if (req.url === '/upload') {
    if (req.method === 'POST') {
      req.setEncoding('binary');

      let body = '';
      const totalBoundary = req.headers['content-type'].split(';')[1];
      const boundary = totalBoundary.split('=')[1];

      req.on('data', (data) => {
        body += data;
      });

      req.on('end', () => {
        console.log(body);
        // 处理body
        // 1.获取image/png的位置
        const payload = qs.parse(body, "\r\n", ": ");
        const type = payload["Content-Type"];

        // 2.开始在image/png的位置进行截取
        const typeIndex = body.indexOf(type);
        const typeLength = type.length;
        let imageData = body.substring(typeIndex + typeLength);

        // 3.将中间的两个空格去掉
        imageData = imageData.replace(/^\s\s*/, '');

        // 4.将最后的boundary去掉
        imageData = imageData.substring(0, imageData.indexOf(`--${boundary}--`));

        fs.writeFile('./foo.png', imageData, 'binary', (err) => {
          res.end("文件上传成功~");
        })
      })
    }
  }
});

server.listen(8000, () => {
  console.log("文件上传服务器开启成功~");
})
```

### 返回文件内容

```js
const http = require('http');
const path = require('path');
const fs = require('fs');
const mime = require('mime-types');

// 静态资源根目录，可以设定为本地的任意有权限目录，放入 a.jpg 测试
const ROOT_DIRECTORY = '/public';

const server = http.createServer((req, res) => {
  const { url } = req;

  const filePath = path.join(ROOT_DIRECTORY, url);

  fs.readFile(filePath, (err, chunk) => {
    if (err) {
      res.writeHead(404, {
        'content-type': 'text/html',
      });
      res.end('文件不存在！');

    } else {
      res.writeHead(200, {
        'content-type': mime.contentType(path.extname(url)),
      });
      res.end(chunk);
    }
  });
});

server.listen(9527, () => {
  console.log('Web Server started at port 9527');
});
```

### 读取电影文件

理论上读取电影文件可以使用和上面一样的代码，但实际执行会发现电影文件在完全读取到内存后才返回给浏览器，这样返回内容耗时极长，

而且电影文件过大的话程序也没有办法处理，

HTTP 协议是支持分段传输的（Transfer-Encoding: chunked），既然 res 是可写流，

可以简单使用 stream 来做到边读取内容边返回给浏览器，而不是一次读取完成后返回

```js
const http = require('http');
const path = require('path');
const fs = require('fs');
const mime = require('mime-types');

// 静态资源根目录
const ROOT_DIRECTORY = '/Users/undefined/node-demo/public';

const server = http.createServer((req, res) => {
  const { url } = req;

  const filePath = path.join(ROOT_DIRECTORY, url);

  fs.access(filePath, fs.constants.R_OK, err => {
    if (err) {
      res.writeHead(404, {
        'content-type': 'text/html',
      });
      res.end('文件不存在！');

    } else {
      res.writeHead(200, {
        'content-type': mime.contentType(path.extname(url)),
      });
      fs.createReadStream(filePath).pipe(res);
    }
  });
});

server.listen(9527, () => {
  console.log('Web Server started at port 9527');
});
```

## Express 框架

```sh
npm i express
```

可以使用 http 内置模块来搭建Web服务器，为什么还要使用框架？

-  原生 http 在进行很多处理时，会较为复杂；

-  有 URL 判断、Method 判断、参数处理、逻辑代码处理等，都需要我们自己来处理和封装；

- 并且所有的内容都放在一起，会非常的混乱；

```js
const express = require('express');

// express其实是一个函数: createApplication
// 返回app
const app = express();

// 监听默认路径
app.get('/', (req, res, next) => {
  res.end("Hello Express");
})

app.post('/', (req, res, next) => {
  
})

app.post('/login', (req, res, next) => {
  res.end("Welcome Back~");
})

// 开启监听
app.listen(8000, () => {
  console.log("express初体验服务器启动成功~");
});
```

Express 是一个路由和中间件的 Web 框架，它本身的功能非常少：

Express 应用程序本质上是一系列中间件函数的调用；

### 中间件

中间件是什么呢？

中间件的本质是传递给 express 的一个回调函数；

可以理解为拦截器过滤

这个回调函数接受三个参数：

- 请求对象（request 对象）；

- 响应对象（response 对象）；

- next 函数（在 express 中定义的用于执行下一个中间件的函数）；

中间件中可以执行哪些任务呢？

-  执行任何代码；

-  更改请求（request）和响应（response）对象；

-  结束请求-响应周期（返回数据）；

-  调用 栈中的下一个中间件；

![Image_018](https://cdn.nlark.com/yuque/0/2023/jpeg/1614731/1683438562413-fe78a4d7-205d-4422-9b4f-ede3b8a54dff.jpeg)

如果当前中间件功能没有结束请求-响应周期，则必须调用 next() 将控制权传递给下一个中间件功能，否则，请求将被挂起。

如何将一个中间件应用到我们的应用程序中呢？

express 主要提供了两种方式：app/router.use 和 app/router.methods；

可以是 app，也可以是 router ;

methods 指的是常用的请求方式，比如： app.get 或 app.post 等；

先学习 use 的用法，因为 methods 的方式本质是 use 的特殊情况；

#### 最普通的中间件

```js
const express = require('express');

const app = express();

// 编写普通的中间件
// use 注册一个中间件(回调函数)，可以在任意请求时执行
// 注册多个中间件，即使是注册多种不同类型的中间件，也是最先注册的中间件先执行，一旦注册的组件没有调用 next() 就会终止
app.use((req, res, next) => {
  console.log("注册了第01个普通的中间件~");
  // 查找下一个匹配的中间件
  next();
});

app.use((req, res, next) => {
  console.log("注册了第02个普通的中间件~");
  next();
});

app.use((req, res, next) => {
  console.log("注册了第03个普通的中间件~");
  // 普通的中间件可以响应请求，结束请求-响应周期，只能有一个 res.end
  res.end("Hello Middleware");
});

app.listen(8000, () => {
  console.log("普通中间件服务器启动成功~");
});
```

打印结果

```
注册了第01个普通的中间件~
注册了第02个普通的中间件~
注册了第03个普通的中间件~
```

当我们多次使用use时

```scss
app.use((req, res, next) => {
  console.log(1)
  next()
  console.log(2)
})

app.use((req, res, next) => {
  console.log(3)
  next()
  console.log(4)
})

app.use((req, res, next) => {
  console.log(5)
  // next()  没有下一个use了，所以这个无效
  next()
  console.log(6)
})
```

它的执行顺序是这样的：

```
1
3
5
6
4
2
```

next 方法会调用下一个 use，next 下面的代码会在下一个 use 执行完再执行

除此之外，koa 的中间件还支持异步，可以使用 async/await

```javascript
app.use(async (ctx, next) => {
  console.log(1)
  await next()
  console.log(2)
})
app.use(async (ctx, next) => {
  console.log(3)
  let p = new Promise((resolve, roject) => {
    setTimeout(() => {
      console.log('3.5')
      resolve()
    }, 1000)
  })
  await p.then()
  await next()
  console.log(4)
  ctx.body = 'hello world'
})
```

```
1
3
//  一秒后
3.5
4
2
```

async 函数返回的是一个 promise，当上一个 use 的 next 前加上 await 关键字，会等待下一个 use 的回调 resolve 了再继续执行代码。

#### path 匹配中间件

```js
const express = require('express');

const app = express();

app.use((req, res, next) => {
  console.log("common middleware01");
  next();
})

// 路径匹配的中间件
app.use('/home', (req, res, next) => {
  console.log("home middleware 01");
});

// 中间插入了一个普通的中间件
app.use((req, res, next) => {
  console.log("common middleware02");
  next();
})

app.use('/home', (req, res, next) => {
  console.log("home middleware 02");
});

app.listen(8000, () => {
  console.log("express初体验服务器启动成功~");
});
```
打印结果

```
common middleware01
home middleware 01
common middleware02
home middleware 02
```

#### path 和 method 匹配中间件

```js
const express = require('express');

const app = express();

// 路径和方法匹配的中间件
app.use((req, res, next) => {
  console.log("common middleware01");
  next();
})

app.get('/home', (req, res, next) => {
  console.log("home path and method middleware01");
});

app.post('/login', (req, res, next) => {
  console.log("login path and method middleware01");
})

app.listen(8000, () => {
  console.log("express初体验服务器启动成功~");
});
```

#### 注册多个中间件

```js
const express = require('express');

const app = express();

app.use((req, res, next) => {
  console.log("common middleware 01");
  next();
});

app.get('/home', (req, res, next) => {
  console.log("home path and method middleware 01");
  next();
})

// 连续注册的中间件也要调用 next() 才会执行下一个中间件
app.get("/home", (req, res, next) => {
  console.log("home path and method middleware 02");
  next();
}, (req, res, next) => {
  console.log("home path and method middleware 03");
  next();
}, (req, res, next) => {
  console.log("home path and method middleware 04");
  res.end("home page");
});

app.listen(8000, () => {
  console.log("express初体验服务器启动成功~");
});
```

客户端发送请求的方式

客户端传递到服务器参数的方法常见的是5种：

- 方式一：通过 get 请求中的 URL 的 params；

- 方式二：通过 get 请求中的 URL 的 query；

- 方式三：通过 post 请求中的 body 的 json 格式（中间件处理）；

- 方式四：通过 post 请求中的 body 的 x-www-form-urlencoded 格式（中间件处理）；

- 方式五：通过 post 请求中的 form-data 格式（第三方中间件处理）；

### req 参数解析 params  和 query

params 以 /xxx/xxx 形式出现

query 以 ?xxx=xxx&xxx=xxx 形式出现 

```js
const express = require('express');

const app = express();

app.get('/products/:id/:name', (req, res, next) => {
  console.log(req.params);
  res.end("商品的详情数据~");
})

app.get('/login', (req, res, next) => {
  console.log(req.query);
  res.end("用户登录成功~");
})

app.listen(8000, () => {
  console.log("普通中间件服务器启动成功~");
});
```

### 中间件应用 json 和 urlencoded解析

```js
const express = require('express');

const app = express();

// 自己编写的json解析
// app.use((req, res, next) => {
//   if (req.headers["content-type"] === 'application/json') {
//     req.on('data', (data) => {
//       const info = JSON.parse(data.toString());
//       req.body = info;
//     })
  
//     req.on('end', () => {
//       next();
//     })
//   } else {
//     next();
//   }
// })


// 使用express提供给我们的body解析
// body-parser: express3.x 内置express框架
// body-parser: express4.x 被分离出去
// body-parser类似功能: express4.16.x 内置成函数
app.use(express.json());
// extended
// true: 那么对urlencoded进行解析时, 它使用的是第三方库: qs
// false: 那么对urlencoded进行解析时, 它使用的是Node内置模块: querystring
app.use(express.urlencoded({extended: true}));

app.post('/login', (req, res, next) => {
  console.log(req.body);
  res.end("Coderwhy, Welcome Back~");
});

app.post('/products', (req, res, next) => {
  console.log(req.body);
  res.end("Upload Product Info Success~");
});

app.listen(8000, () => {
  console.log("express初体验服务器启动成功~");
});
```

### 中间件应用 form-data 解析

```js
const express = require('express');
const multer = require('multer');

const app = express();

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

const upload = multer();

app.use(upload.any());

app.post('/login', (req, res, next) => {
  console.log(req.body);
  res.end("用户登录成功~")
});

app.listen(8000, () => {
  console.log("form-data解析服务器启动成功~")
});
```

### 中间件应用 form-data 上传文件

```js
const path = require('path');

const express = require('express');
const multer = require('multer');

const app = express();

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, './uploads/');
  },
  filename: (req, file, cb) => {
    cb(null, Date.now() + path.extname(file.originalname));
  }
})

const upload = multer({
  // dest: './uploads/'
  storage
});

app.post('/login', upload.any(), (req, res, next) => {
  console.log(req.body);
  res.end("用户登录成功~")
});

app.post('/upload', upload.array('file'), (req, res, next) => {
  console.log(req.files);
  res.end("文件上传成功~");
});

app.listen(8000, () => {
  console.log("form-data解析服务器启动成功~")
});
```

### 中间件应用  请求日志记录

```js
const fs = require('fs');

const express = require('express');
const morgan = require('morgan');

const app = express();

const writerStream = fs.createWriteStream('./logs/access.log', {
  flags: "a+"
})

app.use(morgan("combined", {stream: writerStream}));

app.get('/home', (req, res, next) => {
  res.end("Hello World");
})

app.listen(8000, () => {
  console.log("express初体验服务器启动成功~");
});
```

### 响应数据

end 方法

- 类似于 http 中的 response.end 方法，用法是一致的

json 方法

- json 方法中可以传入很多的类型：object、array、string、boolean、number、null等，它们会被转换成 json 格式返回；

status 方法 

- 用于设置状态码

```js
const express = require('express');
const router = require('./routers/users');

const app = express();

app.get('/products/:id/:name', (req, res, next) => {
  console.log(req.params);
  // req.params => 在数据库中查询真实的商品数据
  res.end("商品的详情数据~");
})

app.get('/login', (req, res, next) => {
  console.log(req.query);

  // 设置响应状态码
  res.status(204);

  // res.type("application/json");
  // res.end(JSON.stringify({name: "why", age: 18}));
  // res.json({name: "why", age: 18})
  // 设置内容
  res.json(["abc", "cba", "abc"]);
});

app.listen(8000, () => {
  console.log("普通中间件服务器启动成功~");
});
```

### 路由的使用

如果我们将所有的代码逻辑都写在app中，那么app会变得越来越复杂：

- 一方面完整的Web服务器包含非常多的处理逻辑；

- 另一方面有些处理逻辑其实是一个整体，我们应该将它们放在一起：比如对users相关的处理


​       获取用户列表；

​       获取某一个用户信息；

​       创建一个新的用户；

​        删除一个用户；

​        更新一个用户；

我们可以使用 express.Router来创建一个路由处理程序：

一个 Router 实例拥有完整的中间件和路由系统；

因此，它也被称为 迷你应用程序（mini-app）

routers\products.js

```js
const express = require('express');

const router = express.Router();

module.exports = router;
```

routers\users.js

```js
const express = require('express');

const router = express.Router();

router.get('/', (req, res, next) => {
  res.json(["why", "kobe", "lilei"]);
});

router.get('/:id', (req, res, next) => {
  res.json(`${req.params.id}用户的信息`);
});

router.post('/', (req, res, next) => {
  res.json("create user success~");
});

module.exports = router;
```

index.js

```js
const express = require('express');
const userRouter = require('./routers/users');
const productRouter = require('./routers/products');

const app = express();

app.use("/users", userRouter);
app.use("/products", productRouter);

app.listen(8000, () => {
  console.log("路由服务器启动成功~");
});
```

### 静态服务器

部署静态资源我们可以选择很多方式：

Node 也可以作为静态资源服务器，并且 express 给我们提供了方便部署静态资源的方法；

```js
const express = require('express');

const app = express();

app.use(express.static('./build'));

app.listen(8000, () => {
  console.log("路由服务器启动成功~");
});
```

### 服务端的错误处理

```js
const express = require('express');

const app = express();

const USERNAME_DOES_NOT_EXISTS = "USERNAME_DOES_NOT_EXISTS";
const USERNAME_ALREADY_EXISTS = "USERNAME_ALREADY_EXISTS";

app.post('/login', (req, res, next) => {
  // 加入在数据中查询用户名时, 发现不存在
  const isLogin = false;
  if (isLogin) {
    res.json("user login success~");
  } else {
    // res.type(400);
    // res.json("username does not exists~")
    next(new Error(USERNAME_DOES_NOT_EXISTS));
  }
})

app.post('/register', (req, res, next) => {
  // 加入在数据中查询用户名时, 发现不存在
  const isExists = true;
  if (!isExists) {
    res.json("user register success~");
  } else {
    // res.type(400);
    // res.json("username already exists~")
    next(new Error(USERNAME_ALREADY_EXISTS));
  }
});

app.use((err, req, res, next) => {
  let status = 400;
  let message = "";
  console.log(err.message);

  switch(err.message) {
    case USERNAME_DOES_NOT_EXISTS:
      message = "username does not exists~";
      break;
    case USERNAME_ALREADY_EXISTS:
      message = "USERNAME_ALREADY_EXISTS~"
      break;
    default: 
      message = "NOT FOUND~"
  }

  res.status(status);
  res.json({
    errCode: status,
    errMessage: message
  })
})

app.listen(8000, () => {
  console.log("路由服务器启动成功~");
});
```

## koa 

```sh
npm i koa
```

koa 是 express 同一个团队开发的一个新的Web框架

```js
const Koa = require('koa');

const app = new Koa();

app.use((ctx, next) => {
  ctx.response.body = "Hello World";
});

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### Koa 初体验

koa 注册的中间件提供了两个参数：

- ctx：上下文（Context）对象；

koa 并没有像 express 一样，将 req 和 res 分开，而是将它们作为 ctx 的属性；

ctx 代表请求的上下文对象；

ctx.request：获取请求对象；

ctx.response：获取响应对象；

- next：本质上是一个 dispatch，类似于之前的 next；


```js
const Koa = require('koa');

const app = new Koa();

app.use((ctx, next) => {
  ctx.response.body = "Hello World";
});

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### Koa 中间件

Koa 通过创建的 app 对象，注册中间件只能通过 use 方法；

Koa 并没有提供methods的方式来注册中间件，也没有提供 path 中间件来匹配路径；

真实开发中我们如何将路径和 method 分离呢？

方式一：根据request自己来判断；

方式二：使用第三方路由中间件；

```js
const Koa = require('koa');

const app = new Koa();

// use 注册中间件
app.use((ctx, next) => {
  if (ctx.request.url === '/login') {
    if (ctx.request.method === 'GET') {
      console.log("来到了这里~");
      ctx.response.body = "Login Success~";
    }
  } else {
    ctx.response.body = "other request~";
  }
});

// 没有提供下面的注册方式
// methods方式: app.get()/.post
// path方式: app.use('/home', (ctx, next) => {})
// 连续注册: app.use((ctx, next) => {
// }, (ctx, next) => {
// })

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### 路由的使用

koa官方并没有给我们提供路由的库，我们可以选择第三方库：koa-router

**我们可以先在 router 下封装一个 user.js 的文件：**

**在 app 中将 router.routes() 注册为中间件：**

注意：allowedMethods用于判断某一个method是否支持：

如果请求 get，那么是正常的请求，因为代码有实现get；

如果请求 put、delete、patch，那么就自动报错：

Method Not Allowed，状态码：405；

如果请求 link、copy、lock，那么就自动报错：

Not Implemented，状态码：501；

```sh
npm install koa-router
```

router\user.js

```js
const Router = require('koa-router');

const router = new Router({prefix: "/users"});

router.get('/', (ctx, next) => {
  ctx.response.body = "User Lists~";
});

router.put('/', (ctx, next) => {
  ctx.response.body = "put request~";
});


module.exports = router;
```

index.js

```js
const Koa = require('koa');

const userRouter = require('./router/user');

const app = new Koa();

app.use((ctx, next) => {
  // ctx.response.body = "Hello World";
  next();
});

app.use(userRouter.routes());
app.use(userRouter.allowedMethods());

app.listen(8000, () => {
  console.log("koa路由服务器启动成功~");
});
```

### 参数解析 query 和 params

请求地址：http://localhost:8000/users/123

获取 params

```js
const Koa = require('koa');

const app = new Koa();
const Router = require('koa-router');

const userRouter = new Router({prefix: '/users'});

userRouter.get('/:id', (ctx, next) => {
  console.log(ctx.request.params);
   ctx.response.body = "Hello World";
})

app.use(userRouter.routes());

app.listen(8000, () => {
  console.log("参数处理服务器启动成功~");
});
```

请求地址：http://localhost:8000/login?username=why&password=123p

获取 query

```js
const app = new Koa();

app.use((ctx, next) => {
  console.log(ctx.request.url);
  console.log(ctx.request.query);
  console.log(ctx.request.params);
  ctx.response.body = "Hello World";
});

app.listen(8000, () => {
  console.log("参数处理服务器启动成功~");
});
```

### 参数解析 json urlencoded formdata

参数解析 json

安装依赖

```sh
npm install koa-bodyparser
```

使用 koa-bodyparser 的中间件

```js
const Koa = require('koa');
const bodyParser = require('koa-bodyparser');

const app = new Koa();

app.use(bodyParser());

app.use((ctx, next) => {
  console.log(ctx.request.body);
  ctx.response.body = "Hello World";
});

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### 参数解析 x-www-form-urlencoded

和 json 是一致的

### 参数解析 form-data

请求地址：http://localhost:8000/login

解析 body 中的数据，我们需要使用 multer

安装依赖

```sh
npm install koa-multer
```

使用 multer中间件

```js
const Koa = require('koa');
const bodyParser = require('koa-bodyparser');
const multer = require('koa-multer');

const app = new Koa();

const upload = multer();

app.use(bodyParser());
app.use(upload.any());

app.use((ctx, next) => {
  // 接受 form-data 方式请求时使用 ctx.req.body  
  console.log(ctx.req.body);
  ctx.response.body = "Hello World";
});

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### multer 上传文件

```js
const Koa = require('koa');
const Router = require('koa-router');
const multer = require('koa-multer');

const app = new Koa();
const uploadRouter = new Router({prefix: '/upload'});

const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, './upload/');
  },
  filename: (req, file, cb) => {
    cb(null, Date.now() + path.extname(file.originalname));
  }
})

const upload = multer({
  storage
});

uploadRouter.post('/avatar', upload.single('avatar'), (ctx, next) => {
  console.log(ctx.req.file);
  ctx.response.body = "上传头像成功~";
});

app.use(uploadRouter.routes());

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### 响应数据

body 将响应主体设置为以下之一：

-  string ：字符串数据
-  Buffer ：Buffer数据
-  Stream ：流数据
-  Object|| Array：对象或者数组
-  null ：不输出任何内容
-  如果 response.status 尚未设置，Koa 会自动将状态设置为 200 或 204

```js
const Koa = require('koa');

const app = new Koa();

app.use((ctx, next) => {
  // ctx.request.query 可以简写为 ctx.query
    
  // 设置内容，可以简写为 ctx.body  
  // ctx.response.body
  // ctx.response.body = "Hello world~"
  // ctx.body = {
  //   name: "coderwhy",
  //   age: 18,
  //   avatar_url: "https://abc.png"
  // };
  // 设置状态码，可以简写为 ctx.status
  // ctx.response.status = 400;
  // ctx.response.body = ["abc", "cba", "nba"];
  // ctx.response.body = "Hello World~";
  ctx.status = 404;
  ctx.body = "Hello Koa~";
});

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### 部署静态资源

koa并没有内置部署相关的功能，所以我们需要使用第三方库：

```sh
npm install koa-static
```

部署的过程类似于express：

```js
const Koa = require('koa');
const staticAssets = require('koa-static');

const app = new Koa();

app.use(staticAssets('./build'));

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```

### 错误处理

```js
const Koa = require('koa');

const app = new Koa();

app.use((ctx, next) => {
  const isLogin = false;
  if (!isLogin) {
    ctx.app.emit('error', new Error("您还没有登录~"), ctx);
  }
});

app.on('error', (err, ctx) => {
  ctx.status = 401;
  ctx.body = err.message;
})

app.listen(8000, () => {
  console.log("koa初体验服务器启动成功~");
});
```









