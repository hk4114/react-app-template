# react webapp template
```sh
npx create-react-app [project name] --template typescript
npm i antd --save
```


- js or ts -> js   ts npx create-react-app my-app --template typescript
- hooks or class -> hooks
- 预处理 css postcss autofixer
- 状态管理 


第一步应该考虑项目是否需要状态管理，实际上大部分项目需要的只是跨组件的通信，而不是管理。
如果你的项目还没上升到需要状态管理的时候，可以考虑选择状态共享库（类似hox）外加 hooks 一把梭

husky + lint

- pages
- components
- services
- store 需要的话
- utils
- types ts
- assets 静态资源 图片等
- tests 需要的话

- [x] 错误边界
- [ ] 请求拦截与响应拦截
- [ ] 配置文件引用别名
- [ ] 配置 husky + lint-staged
- [ ] 工程化，代码格式化 eslint prettier
- [ ] 配置 css 预处理器 sass autofixer 等
- [ ] 动态主题 styled-components
- [ ] 状态管理 hox + hooks?
- [ ] 国际化
- [ ] filter-table


## 初始化项目

```sh
yarn create @vitejs/app vue-vite-template --template
# 选择 vue 回车 => vue-ts 回车
yarn
yarn dev

# 代码规范 eslint 安装
yarn add eslint --dev
# 如果 eslint 安装报错
yarn config set ignore-engines true

# eslint 插件安装
yarn add eslint-plugin-vue --dev
yarn add @typescript-eslint/eslint-plugin --dev
yarn add eslint-plugin-prettier --dev

# typescript parser
yarn add @typescript-eslint/parser --dev

# prettier 支持
yarn add prettier --dev
# 解决 eslint 和 prettier 冲突，以 prettier 的样式规范为准
yarn add eslint-config-prettier --dev
```

1. 项目下新建 .eslintrc.js 配置 eslint 校验规则
2. 项目下新建 .eslintignore 配置忽略检查
3. 项目下新建 .prettier.js 配置 prettier 格式化规则
4. 项目下新建 .prettierignore 配置忽略格式化
5. package.json 配置

```json
{
  "script": {
    "lint": "eslint src --fix --ext .ts,.tsx,.vue,.js,.jsx",
    "prettier": "prettier --write ."
  }
}
```

配置完成后依次执行以下命令，查看格式化效果。

```sh
# eslint 检查
yarn lint
# prettier 自动格式化
yarn prettier
```

6. 配置 husky + lint-staged

安装推荐使用 mrm, 它将根据 package.json 依赖项中的代码质量工具来安装和配置 husky 和 lint-staged。
确保在此之前安装并配置所有代码质量工具，如 Prettier 和 ESlint。

husky 是一个为 git 客户端增加 hook 的工具。安装后，它会自动在仓库中的 .git/ 目录下增加相应的钩子；比如 pre-commit 钩子就会在你执行 git commit 的触发。可以在 pre-commit 中实现一些比如 lint 检查、单元测试、代码美化等操作。

lint-staged，一个仅仅过滤出 Git 代码暂存区文件(被 git add 的文件)的工具。也就是对代码增量部分进行检查，如果对项目全量检查，可能导致项目改动很大。
```sh
npm i mrm -D --registry=https://registry.npm.taobao.org
# mrm 安装 lint-staged 会自动把 husky 一起安装下来
npx mrm lint-staged
```
结合 prettier 代码格式化，修改一下配置
```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,jsx,vue,ts,tsx}": [
      "yarn lint",
      "prettier --write",
      "git add"
    ]
  }
}
```

## 错误边界
组件使用代码
```js
import React, { Component } from 'react';
import ErrorBoundary from './ErrorBoundary';



class BuggyCounter extends Component {
  constructor(props) {
    super(props);
    this.state = { counter: 0 };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(({ counter }) => ({
      counter: counter + 1
    }));
  }

  render() {
    if (this.state.counter === 5) {
      throw new Error('Error!');
    }
    return <h1 onClick={this.handleClick}>{this.state.counter}</h1>;
  }
}

const onReset = () => {
  console.log('已重置')
}

const renderFallback = (props) => {
  return (
    <div>
      出错啦，你可以<button onClick={props.resetErrorBoundary}>重置</button>
    </div>
  )
}

const BuggyCounterWithErrorBoundary = () => {
  return (
    <ErrorBoundary
      onReset={onReset}
      fallbackRender={renderFallback}
      onError={(a, b) => console.log(a, b)}
      // 变更resetKeys实现重置
      resetKeys={+new Date()}
    >
      <BuggyCounter />
    </ErrorBoundary>
  )
}

export default BuggyCounterWithErrorBoundary
```

## lock down the node and yarn version
```json
"engines": {
  "node": "^16.0.0",
  "yarn": "^1.20.0"
},
```

## setup prettier and eslint
