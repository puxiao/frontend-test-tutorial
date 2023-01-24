# 01 Jest基础入门



<br>

### Jest简介

Jest 是由 facebook 开源的一个用于 JavaScript 测试的库，是所有前端单元测试绕不开的一个库。

Jest 官网：https://jestjs.io/zh-Hans/

Jest 仓库：https://github.com/facebook/jest



<br>

**Jest的优点：**

* Jest 适用于所有主流的 JS 框架，例如 Babel、TypeScript、Node、React、Vue、Angular 等
* 无需过多配置，安装即可使用
* 拥有快照功能，适用于大型前端项目
* 采用并行方式进行单元测试，代码效率高
* 仅需要添加 `--coverage` 参数即可生成代码覆盖率报告
* 支持 模拟(mock)数据、模拟函数(mock functions)，方便各种测试
* 测试报错时，提供丰富的上下文内容，方便检查错误根源
* 官网中提供简体中文版文档和教程



<br>

**安装Jest：**

对于使用 create-react-app 创建的 React 项目而言，默认已经自带安装和配置好了 jest 以及 jest 最经常用到的一些周边 NPM 包，例如 `jest-cli`、`jest-diff` 等，我们直接使用即可。

> 毕竟 React 和 Jest 都出自于 facebook



<br>

对于其他 JS 框架，例如 Vue 或 Node.js，则需要自己根据实际情况来手工安装，具体的安装命令和配置和查看：https://jestjs.io/zh-Hans/docs/getting-started

> 我不清楚 Vue 内部自带的单元测试使用的是什么，我几乎不用 Vue，平时主要用 React。
>
> 所以本文的代码示例都是运行在 React 框架中，如果你使用的是 Vue 则可能存在细微差异。



<br>

**Jest配套工具：**

除了 Jest 本身之外，还存在一些 工具(NPM包)，这些工具扩展了 Jest 在某些方面的功能，如果你有需要可选择性的执行安装它们。

常见的有：

* jest-changed-files：用于识别 git 存储库中已修改文件的工具
* jest-diff：以比较容易阅读的方式打印出不同对象之间的差异之处
* jest-docblock：提取和解析 JS 文件顶部的注释
* jest-get-type：用于识别 某个 JS 值的原始类型
* jest-validate：用于验证用户提交的配置工具
* jest-worker：用于并行化测试运算
* pretty-format：以比较容易阅读的方式格式化输出结果对象

> 再说一遍，对于使用 create-react-app 创建的 React 项目而言，已经内置安装了上面的一些工具，如非必要无需自己再安装



<br>

另外，React 项目为了方便进行测试，还自动安装有：

* @testing-library/jest-dom：和 DOM 相关的，可创建 DOM 快照并进行对比
* @testing-library/react：Jest 针对测试 React 提供的一些特定功能
* @testing-library/user-event：提供(接近模拟)用户的一些交互性操作，例如 用户的输入



<br>

好了，至此我们对 Jest 有了一些基础了解，且知道对于 create-react-app 创建的 react 项目而言已经内置了  jest，我们无需做任何安装或配置即可直接使用。

那么接下来就开始学习 Jest 的一些具体用法。



<br>

**特别说明：**

下面开始学习的示例代码，是运行在：

* node.js：18.12.1
* react：18.2.0
* jest：26.6.3
* typescript：4.9.4
* 此外还使用了一些 react 常用的 NPM 包，例如 craco、eslint、prettier、sass 等
* 示例命令中使用 yarn 而非 npm

> 如果你的 react 不是这个版本，或者你使用的是 Vue，也不用担心，绝大多数的示例用法都是相同的。



<br>

### 基础设定(约定)

**.js 或 .ts ？**

首先单元测试 JS 文件本身和普通 JS 文件相同，你可以选择 `.js` 也可以选择使用 `.ts`。

> 对于绝大多数开源项目而言都会使用 .js 而非 .ts，因为考虑到非 TS 项目也能使用。



<br>

**单元测试文件名：xx.test.js 或 xx.test.ts**

通常情况下，都会将单元测试文件命名成 `xx.test.js` 或 `xx.test.js`

当你文件名为这种格式后，jest 默认就会识别到并判定 `xx.test.js` 为单元测试文件。

> 但是也有例外，例如 react 仓库中采用的是 `xx-test.js` 这种格式，不过这种情况下 jest 默认是不认识的，可能需要额外配置，但具体如何配置暂时我也不知道。



<br>

**单元测试文件名 xx：**

通常情况下都会约定使用 被测试 的文件名。例如假定我们有一个名为 `sum.js` 的文件需要测试，那么通常我们会将对应的测试文件命名为 `sum.test.js`



<br>

例如我们使用 create-react-app 创建的项目中，默认就存在 src/App.test.js 这个默认的测试文件。



<br>

**单元测试文件存放目录：**

无论单元测试文件存放在哪个目录中，只要保证文件名遵循 `xx.test.js` 这种格式就可以被 jest 识别。

但是通常都会约定在被测试文件的模块中创建 `__test__` 目录，然后将该模块的各个测试文件存放到这个目录里。

例如假定我们有一个名为 `MyButton` 的自定义组件，该组件所在目录为 `src/components/my-button/`，那么该组件的单元测试目录可以约定为 `src/components/my-button/__test__/`



<br>

**快照目录：**

在单元测试中 Jest 还存在一个概念：快照(snapshots)

快照(snapshot) 可以简单理解为我们自定义组件挂载到网页后的实际 DOM 预期结果。

将来通过单元测试来对比 实际组件渲染结果 与我们提前设定好的快照是否一致，依次判定组件是否通过测试。

我们暂时先不用学习快照的细节和用法，只需先知道以下  2 点即可：

* 通常约定将某模块的快照文件放置在 `__test__/__snapshots__/` 中
* 快照文件后缀并非  `.js` 或 `.ts`，而是 `.snap`



<br>

**如何启动测试？**

我们直接调用 paceage.json 中的命令：

```
yarn test
```



<br>

基础的单元测试文件约定我们了解后，那么接下来只需简单学习几个 测试相关的函数即可编写单元测试文件了。



<br>

### 预期(expect)断言

**名词解释：预期断言**

我们进行的单元测试，本质上就是验证某个函数、组件 在运行之后是否符合我们的预期。

而 "预期断言" 拆分来解释就是：

* 预期：对应的单词为 expect，在 Jest 中对应的是 `expect(value)` 函数，其中参数 `value` 即表示 "执行过后的值"。

  > 大多数时候 value 都是某一个函数的返回结果，例如 `expect(sum(1,2))`， `value` 是`sum(1,2)` 的返回值(计算结果)

* 断言：此处 "断言" 就是它的字面意思，即 "判定是不是"。

  > Jest 为我们提供了很多种 断言 相关的函数，这些函数被称为 "匹配器函数"，例如用于比较两个值是否相等的 `.toBe(value)`
  >
  > Jest 还允许我们通过 `extend(matchers)` 创建自定义匹配器，例如我们可以自定义一个用于检查某函数返回值是否是数字，且该数字是否在 2 ~ 5 之间。

综上所述，**我们可以简单得将 "预期断言" 理解为：将某函数或模块的返回值作为预期值，然后将此预期值与对应的匹配器中的值进行某种条件比较，得出测试结论**。



<br>

**匹配器函数：**

Jest 为我们提供了一些单元测试所需要的常见匹配器函数，具体的可以查阅：

https://jestjs.io/zh-Hans/docs/expect

我们以 `.toBe(value)` 为突破口，学习这一个 匹配器函数，举一反三，就几乎都能弄明白其他匹配器函数怎么用了。

**.toBe(value)：用于将 预期值 与 .toBe(value) 中的 value 进行 === 比较**

* 若运算结果为 true 即表示通过此单元验证
* 若运算结果为 false 即表示 不通过此单元验证



<br>

学习几个基础的单元测试函数。



<br>

**test(name, fn, timeout)：创建一条测试**

* name：类型为字符串，用于描述本条测试的目的
* fn：类型为一个函数，可以是 普通函数 或 箭头函数，通常情况下我们习惯使用 箭头函数，该函数的内部内容为我们要执行的单元测试命令
* timeout：可选参数，类型为数字，即设定执行 fn 的超时时间，通常情况下我们不会做限制



<br>

**describe(name, fn)：将多条 test 命令组合在一起**

在单元测试文件中，只使用 `test()` 命令是没有问题的，但是有时候我们希望将多条 `test()` 组成一个更大的 单元测试模块时，就可以使用 `describe()` 将它们组合在一起。

* name：类型为字符串，用于描述本测试模块的目的

* fn：类型为一个函数，通常会使用箭头函数，该箭头函数的内部内容即是多条 `test()` 或者是另外一个 `describe()`

  > 换句话说 `describe()` 是可以嵌套多层的。



<br>

我们可以将 `test()` 称呼为 "单条测试"，而将 `describe()` 称呼为 "组测试"。



<br>

**关于函数引入的说明：**

由 create-react-app 创建的 react 项目中默认已经配置好了 jest 的全局环境，无论你编写的测试文件是 `xx.test.js` 还是 `xx.test.ts` ，都直接使用这些函数即可，无需引入。

```diff
- import { test, expect } from '@jest/globals'
```



<br>

至此，我们目前学习了：

* 单元测试文件名、存放目录 的一般约定
* 几个基础的测试函数：describe、test、expect、toBe

那么我们就已经可以开始编写第一个单元测试文件了。



<br>

**特别补充：**

使用 create-react-app 创建的 react 项目中，src/App.test.js 这个测试文件中使用了 `@testing-library/react` 这个包，我们暂时先不学习它，但是如果你查看这个测试文件的内容，应该也大致可以看懂了。



<br>

### 测试某个函数示例

假设我们现在定义了一个名为 sum 的函数，用于计算两个数字之和。

> src/utils/sum.ts

```
const sum = (a: number, b: number) => a + b

export default sum
```

那么按照约定，它对应的测试文件路径应该为 `src/utils/__test__/sum.test.js`

使用我们刚才学习过的几个函数 test、expect、toBe，它对应的测试文件代码为：

```
import sum from '../sum'

test(`add 1 + 2 to equal 3`, () => {
    expect(sum(1, 2)).toBe(3)
})
```

或者我们多编写几个测试用例：

```
import sum from '../sum'

describe('test sum()', () => {

    test(`add 1 + 2 to equal 3`, () => {
        expect(sum(1, 2)).toBe(3)
    })

    test(`add 2 + 3 to equal 5`, () => {
        expect(sum(2, 3)).toBe(5)
    })
    
})
```



<br>

假定我们使用的是上面 `describe()` 这个作为测试文件，此时我们可以执行单元测试：

```
yarn test
```



<br>

我们看一下执行单元测试后的输出结果。



<br>

**测试输出的结果：**

```
PASS  src/App.test.js
PASS  src/utils/sum.test.ts       

Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
Snapshots:   0 total
 PASS  src/utils/sum.test.ts
 PASS  src/App.test.js

Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        8.336 s
Ran all test suites related to changed files.

Watch Usage: Press w to show more.
 PASS  src/App.test.js
 PASS  src/utils/__test__/sum.test.ts

Test Suites: 2 passed, 2 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        7.072 s
Ran all test suites related to changed files.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press q to quit watch mode.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press Enter to trigger a test run.

```



<br>

**输出结果解读：**

* Test Suites：可以理解为 "测试方案的套数"，假设一个 `describe()` 中包含 多个 `test()` 也仅仅会被视为 "一条测试方案"

  > Test Suites: 2 passed, 2 total 即告诉我们：有 2 套通过，一共 2 套

* Tests：即单条测试项，一个 `test()` 函数算一条

* Snapshots：快照，由于我们目前还么有添加快照测试，所以 这一项 为 0

* Watch Usage：观测使用选项，所列出来的有：

  * 摁 a 键去运行所有的测试用例
  * 摁 f 键去运行上次测试未通过的用例
  * 摁 q 键退出观察模式 (当你修改代码则会自动运行单元测试)
  * 摁 p 键针对文件名进行增则表达式的筛选
  * 摁 t 键针对测试名进行正则表达式的筛选
  * 摁 Enter 键再次触发单元测试 (执行完毕后摁 w 键再次展示出 `Watch Usage 选择项`)



<br>

**未通过单元测试时输出什么？**

假定我们修改测试代码：

```diff
- expect(sum(2,3)).toBe(5)
+ expect(sum(2,3)).toBe(6)
```

那么自然这条测试是不会被通过的，会在输出结果中定位到这行出错的单元测试代码中。



<br>

至此，我们已经在 React 项目中对 Jest 有了初步的了解和实践。

本文到此结束，下一节我们将系统的学习一遍 除 `.toBe()` 以外的其他匹配器函数。