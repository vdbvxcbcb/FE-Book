# Webpack 打包原理浅析

https://juejin.cn/post/6927678299044839437

package.json

```json
{
  "name": "test",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "@babel/core": "^7.16.5",
    "@babel/parser": "^7.16.6",
    "@babel/traverse": "^7.16.5"
  }
}
```

src/index.js

```js
import message from './message';

console.log(message);
```

src/message.js

```js
import { word } from './word';

const message = `say ${word}`;

export default message;
```

src/word.js

```js
export const word = 'hello';
```

bundle.js

```js
const fs = require("fs")
const path = require("path")
const parser = require("@babel/parser")
const traverse = require("@babel/traverse").default
const babel = require("@babel/core")

const moduleAnalyser = (fileName) => {
  const content = fs.readFileSync(fileName, "utf8")
  const ast = parser.parse(content, { sourceType: "module" })
  const dependencies = {}
  traverse(ast, {
    ImportDeclaration({ node }) {
      const dirname = path.dirname(fileName)
      const newFile = "./" + path.join(dirname, node.source.value + ".js")
      dependencies[node.source.value] = newFile.replace(/\\/g, "/")
    },
  })
  const { code } = babel.transformFromAst(ast, null, {
    presets: ["@babel/preset-env"],
  })
  return { fileName, dependencies, code }
}

const makeDependenciesGraph = (entry) => {
  const entryModule = moduleAnalyser(entry)
  const graphArray = [entryModule]
  for (let i = 0; i < graphArray.length; i++) {
    const item = graphArray[i]
    const { dependencies } = item
    if (dependencies) {
      for (let j in dependencies) {
        const result = moduleAnalyser(dependencies[j])
        graphArray.push(result)
      }
    }
  }
  const graph = {}
  graphArray.forEach((item) => {
    const { fileName, dependencies, code } = item
    graph[fileName] = { dependencies, code }
  })
  return graph
}

const generateCode = (entry) => {
  const graph = makeDependenciesGraph(entry)
  return `
    (function(graph){
      function require(module) {
        function localRequire(relativePath) {
          return require(graph[module].dependencies[relativePath]);
        };
        var exports = {};
        (function(require, exports, code) {
          eval(code);
        })(localRequire, exports, graph[module].code);
        return exports;
      };
      require('${entry}');
    })(${JSON.stringify(graph)});
  `
}

const code = generateCode("./src/index.js")

console.log(eval(code))
```

命令行终端执行 node bunlde.js

输出 hello world
