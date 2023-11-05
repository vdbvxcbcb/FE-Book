

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="./src/element.js"></script>
    <script type="module" src="./src/index.js"></script>
    <script type="module" src="./src/diff.js"></script>
    <script type="module" src="./src/patch.js"></script>
  </body>
</html>

```

index.js

```js
import { createElement, render, renderDom } from './element';
// +++ 引入diff和patch方法
import diff from './diff';
import patch from './patch';
// +++

let virtualDom = createElement('ul', {class: 'list'}, [
    createElement('li', {class: 'item'}, ['周杰伦']),
    createElement('li', {class: 'item'}, ['林俊杰']),
    createElement('li', {class: 'item'}, ['王力宏'])    
]);
console.log(virtualDom);

let el = render(virtualDom);
console.log(el);
renderDom(el, window.app);

// +++
// 创建另一个新的虚拟DOM
let virtualDom2 = createElement('ul', {class: 'list-group'}, [
    createElement('li', {class: 'item active'}, ['七里香']),
    createElement('li', {class: 'item'}, ['一千年以后']),
    createElement('li', {class: 'item'}, ['需要人陪'])    
]);
console.log(virtualDom2);
// diff一下两个不同的虚拟DOM
let patches = diff(virtualDom, virtualDom2);
console.log(patches);
// 将变化打补丁，更新到el
patch(el, patches);
// +++
```

element.js

```js
// 虚拟DOM元素的类，构建实例对象，用来描述DOM
class Element {
    constructor(type, props, children) {
        this.type = type;
        this.props = props;
        this.children = children;
    }
}
// 创建虚拟DOM，返回虚拟节点(object)
function createElement(type, props, children) {
    return new Element(type, props, children);
}

// render方法可以将虚拟DOM转化成真实DOM
function render(domObj) {
    // 根据type类型来创建对应的元素
    let el = document.createElement(domObj.type);
    
    // 再去遍历props属性对象，然后给创建的元素el设置属性
    for (let key in domObj.props) {
        // 设置属性的方法
        setAttr(el, key, domObj.props[key]);
    }
    
    // 遍历子节点
    // 如果是虚拟DOM，就继续递归渲染
    // 不是就代表是文本节点，直接创建
    domObj.children.forEach(child => {
        child = (child instanceof Element) ? render(child) : document.createTextNode(child);
        // 添加到对应元素内
        el.appendChild(child);
    });

    return el;
}

// 设置属性
function setAttr(node, key, value) {
    switch(key) {
        case 'value':
            // node是一个input或者textarea就直接设置其value即可
            if (node.tagName.toLowerCase() === 'input' ||
                node.tagName.toLowerCase() === 'textarea') {
                node.value = value;
            } else {
                node.setAttribute(key, value);
            }
            break;
        case 'style':
            // 直接赋值行内样式
            node.style.cssText = value;
            break;
        default:
            node.setAttribute(key, value);
            break;
    }
}

// 将元素插入到页面内
function renderDom(el, target) {
    target.appendChild(el);
}

export {
    Element,
    createElement,
    render,
    setAttr,
    renderDom
};
```

diff.js

```js
function diff(oldTree, newTree) {
    // 声明变量patches用来存放补丁的对象
    let patches = {};
    // 第一次比较应该是树的第0个索引
    let index = 0;
    // 递归树 比较后的结果放到补丁里
    walk(oldTree, newTree, index, patches);

    return patches;
}

function walk(oldNode, newNode, index, patches) {
    // 每个元素都有一个补丁
    let current = [];

    if (!newNode) { // rule1
        current.push({ type: 'REMOVE', index });
    } else if (isString(oldNode) && isString(newNode)) {
        // 判断文本是否一致
        if (oldNode !== newNode) {
            current.push({ type: 'TEXT', text: newNode });
        }

    } else if (oldNode.type === newNode.type) {
        // 比较属性是否有更改
        let attr = diffAttr(oldNode.props, newNode.props);
        if (Object.keys(attr).length > 0) {
            current.push({ type: 'ATTR', attr });
        }
        // 如果有子节点，遍历子节点
        diffChildren(oldNode.children, newNode.children, patches);
    } else {    // 说明节点被替换了
        current.push({ type: 'REPLACE', newNode});
    }
    // index 标记 class等属性节点和文本节点，0表示
    // 进入 diffChildren() 后跳到 if (oldNode !== newNode)，先设置的是 
    // {   
    //    "2": [
    //        {
    //            "type": "TEXT",
    //             "text": "七里香"
    //        }
    //    ]
    // }
    // 之后退出 diffChildren() 时进行回溯，所以又设置了
    // {
    //     "1": [
    //         {
    //             "type": "ATTR",
    //             "attr": {
    //                 "class": "item active"
    //             }
    //         }
    //     ]
    // }  
    // 所以比较完第一个 li 后 patches 为
    // {
    //   "1": [
    //       {
    //           "type": "ATTR",
    //           "attr": {
    //               "class": "item active"
    //           }
    //       }
    //   ],
    //   "2": [
    //       {
    //           "type": "TEXT",
    //           "text": "七里香"
    //       }
    //   ]
    // }
    // 最后 patches 回溯为
    // {
    //    "0": [
    //        {
    //            "type": "ATTR",
    //            "attr": {
    //                "class": "list-group"
    //            }
    //        }
    //    ],
    //    "1": [
    //        {
    //            "type": "ATTR",
    //            "attr": {
    //                "class": "item active"
    //            }
    //        }
    //    ],
    //    "2": [
    //        {
    //            "type": "TEXT",
    //            "text": "七里香"
    //        }
    //    ],
    //    "4": [
    //        {
    //            "type": "TEXT",
    //            "text": "一千年以后"
    //        }
    //    ],
    //    "6": [
    //        {
    //            "type": "TEXT",
    //            "text": "需要人陪"
    //        }
    //    ]
    // }
    // 当前元素确实有补丁存在
    if (current.length) {
        // 将元素和补丁对应起来，放到大补丁包中
        patches[index] = current;
    }
}

function isString(obj) {
    return typeof obj === 'string';
}

function diffAttr(oldAttrs, newAttrs) {
    let patch = {};
    // 判断老的属性中和新的属性的关系
    for (let key in oldAttrs) {
        if (oldAttrs[key] !== newAttrs[key]) {
            patch[key] = newAttrs[key]; // 有可能还是undefined
        }
    }

    for (let key in newAttrs) {
        // 老节点没有新节点的属性
        if (!oldAttrs.hasOwnProperty(key)) {
            patch[key] = newAttrs[key];
        }
    }
    return patch;
}

// 所有都基于一个序号来实现
let num = 0;

function diffChildren(oldChildren, newChildren, patches) {
    // 比较老的第一个和新的第一个
    oldChildren.forEach((child, index) => {
        walk(child, newChildren[index], ++num, patches);
    });
}

// 默认导出
export default diff;
```

patch.js

```js
import { Element, render, setAttr } from './element';

let allPatches;
let index = 0;  // 默认哪个需要打补丁
/* 
node 为 NodeList 的类数组，浏览器在其原型上添加了 forEach 方法
<ul class="list">
  <li class="item">周杰伦</li>
  <li class="item">林俊杰</li>
  <li class="item">王力宏</li>
</ul> 
*/
function patch(node, patches) {
    allPatches = patches;
    
    // 给某个元素打补丁
    walk(node);
}

function walk(node) {
    let current = allPatches[index++];
    let childNodes = node.childNodes;

    // 先序深度，继续遍历递归每个子节点，直到子节点下再也没有子节点 childNodes，就停止 forEach
    childNodes.forEach(child => walk(child));
    // 为每个节点递归
    if (current) {
        doPatch(node, current); // 打上补丁
    }
}

function doPatch(node, patches) {
    // 遍历所有打过的补丁
    patches.forEach(patch => {
        switch (patch.type) {
            case 'ATTR':
                for (let key in patch.attr) {
                    let value = patch.attr[key];
                    if (value) {
                        setAttr(node, key, value);
                    } else {
                        node.removeAttribute(key);
                    }
                }
                break;
            case 'TEXT':
                node.textContent = patch.text;
                break;
            case 'REPLACE':
                let newNode = patch.newNode;
                newNode = (newNode instanceof Element) ? render(newNode) : document.createTextNode(newNode);
                node.parentNode.replaceChild(newNode, node);
                break;
            case 'REMOVE':
                node.parentNode.removeChild(node);
                break;
            default:
                break;
        }
    });
}

export default patch;
```

