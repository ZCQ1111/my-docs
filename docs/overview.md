<!--
 * @Author: Null Zhao
 * @Date: 2022-03-04 10:07:18
 * @LastEditors: Null Zhao
 * @LastEditTime: 2022-03-11 13:53:52
 * @FilePath: \my-docs\docs\overview.md
 * @Description: 
 * ctrl+alt+i ctrl+win+t win+y
 * Copyright (c) 2022 by null, All Rights Reserved. 
-->
## 测试类型

测试类型，主要是[黑盒测试](https://baike.baidu.com/item/%E9%BB%91%E7%9B%92%E6%B5%8B%E8%AF%95/934030?fr=aladdin)
（验收）和[白盒测试](https://baike.baidu.com/item/%E7%99%BD%E7%9B%92%E6%B5%8B%E8%AF%95)（集成和单元）。
测试通常使用典型的模式编写,
例如[arrange/act/assert](https://docs.microsoft.com/zh-cn/previous-versions/visualstudio/visual-studio-2015/test/unit-test-basics?view=vs-2015&redirectedfrom=MSDN#Anchor_3)
或[given/when/then](https://martinfowler.com/bliki/GivenWhenThen.html)。
这两种风格都很好，所以选择一种你觉得舒服的风格，然后开始编写测试！

## 项目设置
自动化测试套件要求测试运行程序执行所有测试并生成摘要报告。我们使用并推荐[Mocha](https://mochajs.org/#getting-started)。

除了测试运行程序之外，测试套件通常还需要：

- 断言库（我们建议使用 [Should.js](https://shouldjs.github.io/)）。
- 用于进行 HTTP 调用并验证其结果的库（我们建议使用[supertest](https://github.com/visionmedia/supertest)）。
- 用于创建测试替身的库（我们建议使用 [Sinon.JS](https://sinonjs.org/releases/v13/)）。

[@loopback/testlab](https://www.npmjs.com/package/@loopback/testlab) 模块集成了这些软件包，使它们易于与 LoopBack 一起使用。

### 使用Loopback CLI 设置测试基础结构
使用命令生成的 LoopBack 应用程序自带默认值，因此不需要其他测试基础结构设置。
```
lb4 app@loopback/cli@loopback/testlabmocha
```

然后，`package.json`应该看起来像这样：
```
{
  // ...
  "devDependencies": {
    "@loopback/testlab": "^<current-version>",
    "@types/mocha": "^<current-version>",
    "mocha": "^<current-version>"
  },
  "scripts": {
    "test": "mocha --recursive \"dist/test\""
  }
  // ...
}
```
### 目录结构
```
.
└── __tests__
    ├── integration
    ├── acceptance
    ├── README.md
    ├── helpers.ts
    └── unit
        ├── controllers
        └── service
```