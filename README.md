# ELEME Web 产品通用 UBT JS SDK

### Lv.0 基本需求（PV、UV、JSERROR、Performance）

如果需求是「监控访问量」这种程度，则属于这个级别的需求。

这时只需要引入 `ubt.js` 即可

具体的引入方式，推荐使用 `bower`

```bash
bower install git@github.com:eleme/ubt-web.git
```

### Lv.1 事件监控需求（点击监控）

当需求是对某个元素点击进行监控时，就属于这个级别的需求。

首先，产品经理或大数据组应该提供一份文档（如果没提供就找它们要）。

这份文档里会描述哪些地方需要埋点，每个埋点都有一个唯一标识符，这里姑且称为「埋点名」。

这时需要对 HTML 做一些操作，具体操作如下：

```html
<a href="#" ubt-click="埋点名">我是一个需要监控点击的链接</a>

<button ubt-click="埋点名">我是一个需要监控点击的按钮</button>

<div ubt-click="埋点名">我是一个需要监控点击的元素</div>
```

### Lv.2 定制需求 

有时候产品会想出一些神奇的需求，这时候就需要定制。

引入 `ubt.js` 后会再全局定义一个叫 UBT 的对象（注意：Angular 环境不会在全局注册，而是注册一个叫 UBT 的 module 和 factory）。

###### API 描述

```js
var sububt = UBT.bind([type, ] objs...);
var subsububt = sububt.bind(...);
UBT.send([type, ] objs...)
```

###### 用法

```js
// 记录一个 PV
UBT.send('PV');

// 记录一个带数据的 PV
UBT.send('PV', { a: 233, b: 'xxx' });

// 如果觉得每次要带上 PV 参数麻烦可以这么做
var pv = UBT.bindType('PV');
pv.send({ a: 233, b: 'xxx' });

// 如果需要固定一部分数据可以使用
var ab = UBT.bindData({ a: 233, b: 'xxx' });
var ab.send('PV', { c: 'yyy' });

// 当然也可以数据和类型同时固定
var abpv = UBT.bind('PV', { a: 233, b: 'xxx' });
abpv.send();

// 直接的 bind 方法是一种快捷方式，如果第一个参数是字符串自动被作为 type
var pv = UBT.bind('PV');
var pva = UBT.bind('PV', { a: 233 });
var a = UBT.bind({ a: 233 });

// send 方法与 bind 方法接收的参数形式是一样的
// 如果带上 type 会覆盖掉原有的 type
pv.send('XXX', { a: 123 });

// 有时候需要发送一些动态获取的参数，可以在键值对中使用函数
// 这样每次 send 时会调用函数取值
// TODO: 后续可能加入 promise 支持
UBT.send('PV', {
  rnd: function() {
    return Math.random();
  }
});

// 关于 type 的取值是随意的，根据业务需求来决定，建议使用不分词的全大写字母
// type=PV 是一个特殊情况，它会产生 pvhash
// 这个 UBT 组件中有三个全局参数 ssid, pvhash, type
// ssid 是当前用户唯一标识，储存在 cookie 中名为 ubt_ssid，在首次载入时产生
// pvhash 是每次发送 type=PV 时更新的一个值，用于将离散统计对应到 pv 上
// type 就是上面用到各种 type，用于描述当前记录的类型
```

## 开发

本项目采用 ES6 模块打包，已配置 Makefile

```bash
# 生成 ubt.js 及其压缩版本 ubt.min.js
make build
```

```bash
# 进入开发模式，会自动 watch 文件修改，并 build
make dev
```

项目中配置了各种测试，开发时请先跑通测试后再提交代码。

```bash
# 构建 ubt.js
make build
# 初始化配置
cd tests
bower install
```

访问：（可能需要搭一个 http 服务器环境）

```
/tests/test.html
```

如果有功能的增加和调整，请同步相应的测试。
