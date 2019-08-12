---
title: Commit Message规范
tags:
  - 杂谈
copyright: true
comments: true
date: 2019-08-13 01:55:40
categories: 杂谈
photos:
---

在团队开发中，commit message（提交说明）就如同代码注释一样重要。良好的commit message能让团队中的其他成员对你的每次提交的目的、

涉及的代码范围及作用一目了然，方便日常的查询和帮助其他成员更好的帮你Code Review，必要时还能方便的生成Change log。

---
<!--more-->

## Commit message格式

```js
// 格式：<type>(<scope>): <subject>
```

1. type：
`必填` 用于说明commit的类型。总共7个标识：

- feat： 新增feature
- fix: 修复bug
- docs: 仅仅修改了文档，比如README, CHANGELOG, CONTRIBUTE等等
- style: 仅仅修改了空格、格式缩进、变量名等等，不改变代码逻辑
- refactor: 代码重构，没有加新功能或者修复bug
- perf: 优化相关，比如提升性能、体验
- test: 测试用例，包括单元测试、集成测试等
- chore: 改变构建流程、或者增加依赖库、工具等
- revert: 回滚到上一个版本

2. scope：
`可选` scope用于说明 commit 影响的范围，比如数据层、控制层、视图层或者目录甚至文件等等，视项目不同而不同。

3. subject：
`必填` subject是 commit 目的的简短描述，不超过50个字符。

- 约定好commit message的语言，对我们来说最好使用中文
- 最好以动词开头（如使用英文请使用第一人称现在时，并且第一个字母小写）
- `<scope>`之后的冒号后面留一个英文输入法的空格
- 结尾不加句号或其他标点符号
- 若此次commit是解决某个issue应该在行末尾注明并加链接，如：...(#101)

## Commit message检查工具

1. [commitizen](https://github.com/commitizen/cz-cli): 一个撰写合格 Commit message 的工具；

2. [validate-commit-msg](https://github.com/kentcdodds/validate-commit-msg): 用于检查 Node 项目的 Commit 

message 是否符合格式。
