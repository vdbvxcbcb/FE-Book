



```sh
npm install -D eslint 
npm install -D prettier
npm install -D eslint-config-prettier # eslint 兼容的插件
npm install -D eslint-plugin-prettier # eslint 的 prettier
npm install -D eslint-plugin-vue # vue 官方推荐的 eslint 插件
npm install stylelint postcss postcss-less postcss-html stylelint-config-prettier stylelint-config-recommended-less stylelint-config-standard stylelint-config-standard-vue stylelint-less stylelint-order -D
```

.eslintrc.js

```js
module.exports = {
  root: true,
  env: {
    browser: true, // 浏览器环境中的全局变量
    node: true, // Node.js 全局变量和 Node.js 作用域
    es6: true, // 启用除了 modules 以外的所有 ECMAScript 6 特性（该选项会自动设置 ecmaVersion 解析器选项为 6）
  },
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser',
    ecmaVersion: 'latest',
    sourceType: 'module',
    jsxPragma: 'React', // 支持 React 的 JSX 语法
    ecmaFeatures: {
      tsx: true, // 允许解析 tsx
      jsx: true,
    },
  },
  // 继承通用规则
  extends: [
    'plugin:vue/vue3-recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:prettier/recommended', // 一定要放在最后。因为 extends 中后引入的规则会覆盖前面的规则。
  ],
  // 自定义规则
  rules: {
    // 防止 <template> 中使用的 <script setup> 变量被标记为未使用
    'vue/script-setup-uses-vars': 'error',
    // 禁用 eslint 规则
    '@typescript-eslint/ban-ts-ignore': 'off',
    // 要求函数和类方法显式返回类型
    '@typescript-eslint/explicit-function-return-type': 'off',
    // 禁止使用 any 类型
    '@typescript-eslint/no-explicit-any': 'off',
    // 除了 import 外禁止使用 requires
    '@typescript-eslint/no-var-requires': 'off',
    // 不允许空函数
    '@typescript-eslint/no-empty-function': 'off',
    // 为自定义事件名称强制使用特定大小写
    'vue/custom-event-name-casing': 'off',
    // 在定义之前禁止使用变量
    'no-use-before-define': 'off',
    '@typescript-eslint/no-use-before-define': 'off',
    // 禁止 @ts-<directive>使用注释或要求在指令后进行描述
    '@typescript-eslint/ban-ts-comment': 'off',
    // 禁止使用特定类型
    '@typescript-eslint/ban-types': 'off',
    '@typescript-eslint/no-non-null-assertion': 'off',
    // 要求导出函数和类的公共方法显式返回参数类型
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    // 禁止未使用的变量
    '@typescript-eslint/no-unused-vars': [
      'error',
      {
        argsIgnorePattern: '^_',
        varsIgnorePattern: '^_',
      },
    ],
    'no-unused-vars': [
      'error',
      {
        argsIgnorePattern: '^_',
        varsIgnorePattern: '^_',
      },
    ],
    // 在函数括号之前强制执行一致的间距
    'space-before-function-paren': 'off',
    // 强制执行属性顺序
    'vue/attributes-order': 'off',
    // 强制每个组件都应该在自己的文件中
    'vue/one-component-per-file': 'off',
    // 在标签的右括号之前要求禁止换行
    'vue/html-closing-bracket-newline': 'off',
    // 强制每行的最大属性数
    'vue/max-attributes-per-line': 'off',
    // 在多行元素的内容之前和之后需要换行符
    'vue/multiline-html-element-content-newline': 'off',
    // 在单行元素的内容之前和之后需要换行符
    'vue/singleline-html-element-content-newline': 'off',
    // 对模板中的自定义组件强制执行属性命名样式
    'vue/attribute-hyphenation': 'off',
    // 需要 props 的默认值
    'vue/require-default-prop': 'off',
    // 需要显式声明 emits
    'vue/require-explicit-emits': 'off',
    // 配置自闭合 html 标签风格
    'vue/html-self-closing': [
      'error',
      {
        html: {
          void: 'always', // void 元素在没有内容时自动关闭
          normal: 'never', // 除了 void 元素禁止自动关闭
          component: 'always', // 自定义组件的样式 要求在没有内容的元素处自动关闭
        },
        svg: 'always', // SVG 元素在没有内容时自动关闭
        math: 'always', // MathML 元素在没有内容时自动关闭
      },
    ],
    // 是否开启组件命名规则校验（强制要求组件名称始终为多个单词，以驼峰或'-'链接的命名规则）
    'vue/multi-word-component-names': 'off',
  },
};
```

.prettierrc.js

```js
module.exports = {
  printWidth: 160, // 超过多少字符强制换行
  semi: true, // 代码结尾是否加分号
  vueIndentScriptAndStyle: true, // vue 文件中的 script 和 style 标签内是否使用缩进
  singleQuote: true, // 是否使用单引号
  bracketSpacing: true, // 大括号内两边是否加空格
  trailingComma: 'all', // 多行语法中末尾会加逗号
  proseWrap: 'preserve', // 当超出 print width 时按照文件原样换行
  htmlWhitespaceSensitivity: 'ignore', // 根据显示样式决定 html 要不要换行
  jsxBracketSameLine: false, //在 jsx 中把 '>' 是否单独放一行
  endOfLine: 'auto', // 保持系统文件换行格式
  useTabs: false, // 使用空格缩进行
};
```

.eslintignore

```
*.sh
node_modules
*.md
*.woff
*.ttf
.vscode
.idea
dist
/public
/docs
.husky
.local
/bin
Dockerfile
```

.prettierignore

```
/dist/*
.local
.output.js
/node_modules/**

**/*.svg
**/*.sh

/public/*
```

.stylelintrc.js

```js
module.exports = {
  extends: ['stylelint-config-standard', 'stylelint-config-prettier', 'stylelint-config-recommended-less', 'stylelint-config-standard-vue'],
  plugins: ['stylelint-order'],
  // 不同格式的文件指定自定义语法
  overrides: [
    {
      files: ['**/*.(less|css|vue|html)'],
      customSyntax: 'postcss-less',
    },
    {
      files: ['**/*.(html|vue)'],
      customSyntax: 'postcss-html',
    },
  ],
  ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts', '**/*.json', '**/*.md', '**/*.yaml'],
  rules: {
    'no-descending-specificity': null, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器
    'selector-pseudo-element-no-unknown': [
      true,
      {
        ignorePseudoElements: ['v-deep'],
      },
    ],
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['deep'],
      },
    ],
    // 指定样式的排序
    'order/properties-order': [
      'position',
      'top',
      'right',
      'bottom',
      'left',
      'z-index',
      'display',
      'justify-content',
      'align-items',
      'float',
      'clear',
      'overflow',
      'overflow-x',
      'overflow-y',
      'padding',
      'padding-top',
      'padding-right',
      'padding-bottom',
      'padding-left',
      'margin',
      'margin-top',
      'margin-right',
      'margin-bottom',
      'margin-left',
      'width',
      'min-width',
      'max-width',
      'height',
      'min-height',
      'max-height',
      'font-size',
      'font-family',
      'text-align',
      'text-justify',
      'text-indent',
      'text-overflow',
      'text-decoration',
      'white-space',
      'color',
      'background',
      'background-position',
      'background-repeat',
      'background-size',
      'background-color',
      'background-clip',
      'border',
      'border-style',
      'border-width',
      'border-color',
      'border-top-style',
      'border-top-width',
      'border-top-color',
      'border-right-style',
      'border-right-width',
      'border-right-color',
      'border-bottom-style',
      'border-bottom-width',
      'border-bottom-color',
      'border-left-style',
      'border-left-width',
      'border-left-color',
      'border-radius',
      'opacity',
      'filter',
      'list-style',
      'outline',
      'visibility',
      'box-shadow',
      'text-shadow',
      'resize',
      'transition',
    ],
  },
};
```

.vscode\settings.json

```json
{
  // 开启自动修复
  "editor.codeActionsOnSave": {
    "source.fixAll": false,
    "source.fixAll.eslint": true,
    "source.fixAll.stylelint": true
  },
  // 保存的时候自动格式化
  "editor.formatOnSave": true,
  // 默认格式化工具选择prettier
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  // 配置该项，新建文件时默认就是space：2
  "editor.tabSize": 2,
  // stylelint校验的文件格式
  "stylelint.validate": ["css", "less", "vue", "html"]
}
```

package.json

```json
"scripts": {
    "prepare": "husky install",
    "dev": "vite",
    "build": "vue-tsc --noEmit && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext .vue,.js,.ts,.jsx,.tsx --fix",
    "format": "prettier --write \"./**/*.{html,vue,ts,js,json,md}\"",
    "lint:style": "stylelint \"./**/*.{css,less,vue,html}\" --fix"
},
```

vite.config.ts

```ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import vueJsx from '@vitejs/plugin-vue-jsx';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), vueJsx()],
  css: {
    preprocessorOptions: {
      less: {
        javascriptEnabled: true,
      },
    },
  },
});
```

