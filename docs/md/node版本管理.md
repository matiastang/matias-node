<!--
 * @Author: tangdaoyong
 * @Date: 2021-05-26 10:26:22
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-05-26 11:02:46
 * @Description: node版本管理
-->
# node版本管理

自动管理`node`版本的方式主要有两种：

1. 使用`n`管理`node`版本。
2. 使用`nvm`管理`node`版本。

## node版本

[node 历史版本](https://nodejs.org/zh-cn/download/releases/)

## n

[n github地址](https://github.com/tj/n)

`n`是`node`一个模块，可以用来管理和切换`node`版本，其作者是`TJ Holowaychuk`（出名的`Express`框架作者），使用非常之简单。

具体使用请参照[`n.md`](https://github.com/matiastang/matias-npm-yarn-pnpm/blob/main/docs/md/node%E7%89%88%E6%9C%AC%E7%AE%A1%E7%90%86/n.md)

## nvm

[nvm github地址](https://github.com/creationix/nvm)

`nvm`全称`Node Version Manager`，不同于 `n`，`nvm` 不是一个 `npm package`，而是一个独立软件包。这意味着我们需要单独使用它的安装逻辑。
```js
$ wget -qO- https://raw.github.com/creationix/nvm/v0.4.0/install.sh | sh
```
独立的安装包，因此当你机器上没有安装node时仍可以使用它。

具体使用请参照[`nvm.md`](https://github.com/matiastang/matias-npm-yarn-pnpm/blob/main/docs/md/node%E7%89%88%E6%9C%AC%E7%AE%A1%E7%90%86/nvm.md)

## n 和 nvm 区别

1. 安装简易度。`nvm` 安装起来显然是要麻烦不少；`n` 这种安装方式更符合 `node` 的惯性思维。
2. 系统支持。注意， `n` 不支持 `Windows`。
3. 对全局模块的管理。`n` 对全局模块毫无作为，因此有可能在切换了 `node` 版本后发生全局模块执行出错的问题；`nvm` 的全局模块存在于各自版本的沙箱中，切换版本后需要重新安装，不同版本间也不存在任何冲突。
4. 关于 `node` 路径。`n` 是万年不变的 `/usr/local/bin`；`nvm` 需要手动指定路径。
