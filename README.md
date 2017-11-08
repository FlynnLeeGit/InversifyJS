# InversifyJS

[![Join the chat at https://gitter.im/inversify/InversifyJS](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/inversify/InversifyJS?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://secure.travis-ci.org/inversify/InversifyJS.svg?branch=master)](https://travis-ci.org/inversify/InversifyJS)
[![Windows Build status](https://ci.appveyor.com/api/projects/status/cd9ekn86p8y2t7h4/branch/master?svg=true)](https://ci.appveyor.com/project/remojansen/inversifyjs/branch/master)
[![Test Coverage](https://codeclimate.com/github/inversify/InversifyJS/badges/coverage.svg)](https://codeclimate.com/github/inversify/InversifyJS/coverage)
[![npm version](https://badge.fury.io/js/inversify.svg)](http://badge.fury.io/js/inversify)
[![Dependencies](https://david-dm.org/inversify/InversifyJS.svg)](https://david-dm.org/inversify/InversifyJS#info=dependencies)
[![img](https://david-dm.org/inversify/InversifyJS/dev-status.svg)](https://david-dm.org/inversify/InversifyJS/#info=devDependencies)
[![img](https://david-dm.org/inversify/InversifyJS/peer-status.svg)](https://david-dm.org/inversify/InversifyJS/#info=peerDependenciess)
[![Known Vulnerabilities](https://snyk.io/test/github/inversify/InversifyJS/badge.svg)](https://snyk.io/test/github/inversify/InversifyJS)
[![Twitter Follow](https://img.shields.io/twitter/follow/InversifyJS.svg?style=flat&maxAge=86400)](https://twitter.com/inversifyjs)

[![NPM](https://nodei.co/npm/inversify.png?downloads=true&downloadRank=true)](https://nodei.co/npm/inversify/)
[![NPM](https://nodei.co/npm-dl/inversify.png?months=9&height=3)](https://nodei.co/npm/inversify/)

![](https://raw.githubusercontent.com/inversify/inversify.github.io/master/img/cover.jpg)

一个由 TypeScript 为 Javascript 和 NodeJs 运行编写的功能强大且轻量级的控制反转容器.

## 介绍
InversifyJS 是为 Typescript 和 Javascript 编写的一个控制反转 (IoC) 容器, 它仅有 4KB 大小, 非常的轻量.
容器通过一个类构造器来识别和注入他的依赖项.InversifyJS 的API接口非常优惠，并鼓励使用面向对象和IOC惯例进行编程.
当前文档为该项目最新版中文文档翻译,切换到([原项目页面](https://github.com/inversify/InversifyJS/))

## 初衷
JavaScript 现在已经能够很好的支持面向对象的继承特性. 这些特性设计非常有价值,但同时使用的过程中也相当的
[危险](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4).

我需要一个更好的面向对象设计方法 ([面向对象设计](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)), [合成复用原则](https://en.wikipedia.org/wiki/Composition_over_inheritance), etc.) to protect ourselves from these threats. The problem is that OO design is difficult and that is exactly why we created InversifyJS.

InversifyJS 是一个帮助 JavaScript 开发者使用更好地方式进行面向对象程序设计的工具.

## 信条
InversifyJS 定制了以下四个目标:

1. 运行 JavaScript 开发者编写遵循 SOLID 原则的代码.

2. 促进并鼓励开发遵守Ioc设计惯例以及最佳的面向对象设计.

3. 尽可能少的增加运行时的额外开销.

4. 提供整套 [最佳实践](./wiki-cn/ecosystem.md).

## 证言

**[Nate Kohari](https://twitter.com/nkohari)** - Author of [Ninject](https://github.com/ninject/Ninject)

> *"Nice work! I've taken a couple shots at creating DI frameworks for JavaScript and TypeScript, but the lack of RTTI really hinders things.* 
> *The ES7 metadata gets us part of the way there (as you've discovered). Keep up the great work!"*

**[Michel Weststrate](https://twitter.com/mweststrate)** - Author of [MobX](https://github.com/mobxjs/mobx)
> *Dependency injection like InversifyJS works nicely*

## 安装

你可以通过 npm 来获得最新的发行版和类型定义：

```
$ npm install inversify reflect-metadata --save
```

Inversify 的 npm 包中已经包含了 InversifyJS 的类型定义.

> :warning: **提示!** InversifyJS 要求 TypeScript >= 2.0 版本,并且需要在项目的 `tsconfig.json` 文件中对 `experimentalDecorators`, `emitDecoratorMetadata`, `types` and `lib` 编译选项进行设置

```js
{
    "compilerOptions": {
        "target": "es5",
        "lib": ["es6"],
        "types": ["reflect-metadata"],
        "module": "commonjs",
        "moduleResolution": "node",
        "experimentalDecorators": true,
        "emitDecoratorMetadata": true
    }
}
```

InversifyJS 要求 JavaScript 引擎能够支持以下特性:

- [Reflect metadata](https://rbuckton.github.io/reflect-metadata/)
- [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) (Only required if using [provider injection](./wiki-cn/provider_injection.md))
- [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) (Only required if using [activation handlers](./wiki-cn/activation_handler.md))

如果你的运行环境中有一个或多个特性无法支持,你需要通过手工导入一个 shim 或者 polyfill.

> :warning: **`reflect-metadata` polyfill 在你的整个应用程序中仅能够被导入一次** 因为反射对象是一个全局单例. 更多的详细细节可以查看 [这里](https://github.com/inversify/InversifyJS/issues/262#issuecomment-227593844).

你可以在wiki查看 [环境支持与集成](./wiki-cn/environment.md)
页面和我们提供的[最佳实践案例](https://github.com/inversify/inversify-basic-example) 来了解更多.

## 基础入门

让我们来看一下通过 TypeScript 来调用 InversifyJS API接口的最基础用法:

### 步骤 1: 创建你的接口和类型

我们的目标是编写一份遵循 [控制反转原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle) 的代码.
这意味着我们必须 "依赖于抽象接口而非依赖于实现".让我们先来进行一些接口 (抽象) 的声明 .

```ts
// 文件 interfaces.ts

interface Warrior {
    fight(): string;
    sneak(): string;
}

interface Weapon {
    hit(): string;
}

interface ThrowableWeapon {
    throw(): string;
}
```

InversifyJS 在运行时需要使用类型作为表示法. 我们可以使用 symbols 作为标示符, 也可以使用类或者字符串.
 
```ts
// 文件 types.ts

const TYPES = {
    Warrior: Symbol("Warrior"),
    Weapon: Symbol("Weapon"),
    ThrowableWeapon: Symbol("ThrowableWeapon")
};

export { TYPES };

```

> **提示**: 一般来说我们建议使用 Symbols , 不过 InversifyJS 也支持使用类或者字符串字面值 (请参阅特征特征介绍部分的章节了解详细信息).

### 步骤 2: 通过使用 `@injectable` 和 `@inject` 装饰器（注解）来进行依赖项声明

让我们接着来声明一些类（结构）. 这些类会实现我们刚刚定义的接口,所有的类都必须使用 `@injectable` 装饰器进行注释.

当一个类是实现接口后, 我们还需要使用 `@inject` 注解去运行时的依赖.在这个例子中我们使用 Symbols `Symbol("Weapon")` and `Symbol("ThrowableWeapon")` 作为运行标识符.

```ts
// 文件 entities.ts

import { injectable, inject } from "inversify";
import "reflect-metadata";
import { Weapon, ThrowableWeapon, Warrior } from "./interfaces"
import { TYPES } from "./types";

@injectable()
class Katana implements Weapon {
    public hit() {
        return "cut!";
    }
}

@injectable()
class Shuriken implements ThrowableWeapon {
    public throw() {
        return "hit!";
    }
}

@injectable()
class Ninja implements Warrior {

    private _katana: Weapon;
    private _shuriken: ThrowableWeapon;

    public constructor(
	    @inject(TYPES.Weapon) katana: Weapon,
	    @inject(TYPES.ThrowableWeapon) shuriken: ThrowableWeapon
    ) {
        this._katana = katana;
        this._shuriken = shuriken;
    }

    public fight() { return this._katana.hit(); }
    public sneak() { return this._shuriken.throw(); }

}

export { Ninja, Katana, Shuriken };
```

如果你喜欢也可以使用属性注入的方式而不是都同个构造函数进行注入,这样可以不必声明构造函数

```ts
@injectable()
class Ninja implements Warrior {
    @inject(TYPES.Weapon) private _katana: Weapon;
    @inject(TYPES.ThrowableWeapon) private _shuriken: ThrowableWeapon;
    public fight() { return this._katana.hit(); }
    public sneak() { return this._shuriken.throw(); }
}
```

### 步骤 3: 创建容器并进行配置

我们建议创建一个名为 `inversify.config.ts` 的文件. 这是在项目中唯一有耦合的地方.
除此之外的任何地方都不应该包含对其他类的引用！

```ts
// 文件 inversify.config.ts

import { Container } from "inversify";
import TYPES from "./types";
import { Warrior, Weapon, ThrowableWeapon } from "./interfaces";
import { Ninja, Katana, Shuriken } from "./entities";

const myContainer = new Container();
myContainer.bind<Warrior>(TYPES.Warrior).to(Ninja);
myContainer.bind<Weapon>(TYPES.Weapon).to(Katana);
myContainer.bind<ThrowableWeapon>(TYPES.ThrowableWeapon).to(Shuriken);

export { myContainer };
```

### 步骤 4: 解析依赖

你可以使用 `Container` 类提供的 `get<T>` 方法去解析依赖.
记住,你只有在编写 [根目录](http://blog.ploeh.dk/2011/07/28/CompositionRoot/) 时才这么做
Remember that you should do this only in your
以此避免出现 [服务定位器反模式](http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/).

```ts
import { myContainer } from "./inversify.config";

const ninja = myContainer.get<Warrior>(TYPES.Warrior);

expect(ninja.fight()).eql("cut!"); // true
expect(ninja.sneak()).eql("hit!"); // true
```

正如我们所见 `Katana` 和 `Shuriken` 已经被成功的解析和注入到 `Ninja`对象中.

InversifyJS 也支持没有 TypeScript 的情况下直接在 ES5 和 ES6 中运行.
转到 [**JavaScript 案例**](./wiki-cn/basic_js_example.md) to learn more!

## 以下是 InversifyJS 的功能以及 API 接口
让我们仔细阅读 InversifyJS 的特性!

- [对类的支持](./wiki-cn/classes_as_id.md)
- [对 Symbols 的支持](./wiki-cn/symbols_as_id.md)
- [容器 API 接口](./wiki-cn/container_api.md)
- [声明容器模块](./wiki-cn/container_modules.md)
- [容器快照](./wiki-cn/container_snapshots.md)
- [依赖项的 scope 作用域管理](./wiki-cn/scope.md)
- [声明可选属性](./wiki-cn/optional_dependencies.md)
- [常量或动态值的注入](./wiki-cn/value_injection.md)
- [类的注入](./wiki-cn/constructor_injection.md)
- [工厂的注入](./wiki-cn/factory_injection.md)
- [自动工厂](./wiki-cn/auto_factory.md)
- [服务提供者的注入 (异步工厂)](./wiki-cn/provider_injection.md)
- [处理器激活](./wiki-cn/activation_handler.md)
- [构造器注解](./wiki-cn/post_construct.md)
- [中间件](./wiki-cn/middleware.md)
- [多重注入](./wiki-cn/multi_injection.md)
- [标签绑定](./wiki-cn/tagged_bindings.md)
- [创建你自己的装饰标签](./wiki-cn/custom_tag_decorators.md)
- [名称绑定](./wiki-cn/named_bindings.md)
- [默认标签](./wiki-cn/default_targets.md)
- [分层依赖注入系统的支持](./wiki-cn/hierarchical_di.md)
- [上下文绑定 & @targetName](./wiki-cn/contextual_bindings.md)
- [属性注入](./wiki-cn/property_injection.md)
- [循环依赖](./wiki-cn/circular_dependencies.md)
- [继承](./wiki-cn/inheritance.md)

更多详细内容请参考 [wiki页面](./wiki-cn/readme.md) .

## 生态系统

我们也在努力为开发提供更多的最佳时间方案:

- [中间件扩展](./wiki-cn/ecosystem.md#extensions).
- [开发工具集](./wiki-cn/ecosystem.md#development-tools).
- [项目案例](./wiki-cn/ecosystem.md#examples).

请查看Wiki的 [生态系统页面](./wiki-cn/ecosystem.md) to learn more.

## 支持
If you are experience any kind of issues we will be happy to help. You can report an issue using the [issues page](https://github.com/inversify/InversifyJS/issues) or the [chat](https://gitter.im/inversify/InversifyJS). You can also ask questions at [Stack overflow](http://stackoverflow.com/tags/inversifyjs) using the `inversifyjs` tag.

If you want to share your thoughts with the development team or join us you will be able to do so using the [official the mailing list](https://groups.google.com/forum/#!forum/inversifyjs). You can check out the
[wiki](./wiki-cn/readme.md) to learn more about InversifyJS internals.

## 致谢

Thanks a lot to all the [contributors](https://github.com/inversify/InversifyJS/graphs/contributors), all the developers out there using InversifyJS and all those that help us to spread the word by sharing content about InversifyJS online. Without your feedback and support this project would not be possible.

## 授权

License under the MIT License (MIT)

Copyright © 2015-2016 [Remo H. Jansen](http://www.remojansen.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. 

IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
