# 依赖项的生命周期管理

默认情况下 InversifyJS 通过创建临时对象的实现提供依赖, 但你也可以使用单例或者请求声明周期的对象:

```ts
container.bind<Shuriken>("Shuriken").to(Shuriken).inTransientScope(); // Default
container.bind<Shuriken>("Shuriken").to(Shuriken).inSingletonScope();
container.bind<Shuriken>("Shuriken").to(Shuriken).inRequestScope();
```

## 关于 `inSingletonScope（单例作用域）`

以下是若干种可用的绑定方式:

```ts
interface BindingToSyntax<T> {
    to(constructor: { new (...args: any[]): T; }): BindingInWhenOnSyntax<T>;
    toSelf(): BindingInWhenOnSyntax<T>;
    toConstantValue(value: T): BindingWhenOnSyntax<T>;
    toDynamicValue(func: (context: Context) => T): BindingWhenOnSyntax<T>;
    toConstructor<T2>(constructor: Newable<T2>): BindingWhenOnSyntax<T>;
    toFactory<T2>(factory: FactoryCreator<T2>): BindingWhenOnSyntax<T>;
    toFunction(func: T): BindingWhenOnSyntax<T>;
    toAutoFactory<T2>(serviceIdentifier: ServiceIdentifier<T2>): BindingWhenOnSyntax<T>;
    toProvider<T2>(provider: ProviderCreator<T2>): BindingWhenOnSyntax<T>;
}
```

关于范围的理解,我们可以分成两组进行解读:

- 绑定给将来进行注入的是一个 `对象（object）`
- 绑定给将来进行注入的是一个 `函数（function）`

### 绑定给将来进行注入的是一个 `对象（object）`

这组绑定方式有以下两种类型:

```ts
interface BindingToSyntax<T> {
    to(constructor: { new (...args: any[]): T; }): BindingInWhenOnSyntax<T>;
    toSelf(): BindingInWhenOnSyntax<T>;
    toConstantValue(value: T): BindingWhenOnSyntax<T>;
    toDynamicValue(func: (context: Context) => T): BindingInWhenOnSyntax<T>;
}
```

`临时作用域范围（inTransientScope）` 是我们能够选择的默认方式, 例如此处 `toConstantValue` 总是使用 `临时作用域范围（inTransientScope）`.

当我们第一次调用调用 `container.get` 方法, 我们使用 `to`, `toSelf` 而不是 `toDynamicValue` , 此时 InversifyJS 对象会尝试去生成一个对象实例或者使用构造函数或者使用动态工厂来进行生成. 如果这个作用域被设置为 `单例（inSingletonScope）` 的话, 该对象将会被缓存. 而我们通过相同的资源ID调用 `container.get` 方法时, 如果选择的是 `inSingletonScope` 方式, InversifyJS 会尝试从缓存中获取依赖项.

注意， 一个类可以有若干的依赖关系，而动态属性值可以根据上下文的其他渠道中获得. 在他们各自的依赖树中，这些依赖项也行并非单例.

### 绑定给将来进行注入的是一个 `函数（function）`

这组绑定方式有以下这几种类型:

```ts
interface BindingToSyntax<T> {
    toConstructor<T2>(constructor: Newable<T2>): BindingWhenOnSyntax<T>;
    toFactory<T2>(factory: FactoryCreator<T2>): BindingWhenOnSyntax<T>;
    toFunction(func: T): BindingWhenOnSyntax<T>;
    toAutoFactory<T2>(serviceIdentifier: ServiceIdentifier<T2>): BindingWhenOnSyntax<T>;
    toProvider<T2>(provider: ProviderCreator<T2>): BindingWhenOnSyntax<T>;
}
```

我们无法选择该类型的绑定作用域范围 (工厂函数 `function`) 总是单例形式. 然而工厂函数的内部实现可以返回单例也可以不是单例.

例如,以下的代码中我们绑定了一个工厂,这个工厂始终都是单例的:

```ts
container.bind<interfaces.Factory<Katana>>("Factory<Katana>").toAutoFactory<Katana>("Katana");
```

然而,该工厂函数返回的值未必是单例:

```ts
container.bind<Katana>("Katana").to(Katana).inTransientScope();
// or
container.bind<Katana>("Katana").to(Katana).inSingletonScope();
```

## 关于 `请求作用域（inRequestScope）`

inRequestScope 类型是一种特殊的单立刑事.

- `inSingletonScope` 类型会创建一个单例，这单例在整个类型绑定的声明周期内都有效. This means that the `inSingletonScope` 这意味着我们可以通过使用 `container.unbind` 方法将绑定对象从内存中移除.

- `inRequestScope` 会在一次调用 `contaner.get`, `container.getTagged` 或 `container.getNamed` 方法时创建一个单例,直到方法调用结束时销毁. 对其中一个方法的每个调用都将解析一个根依赖项及其所有子依赖项. 在内部维护这一个被 InversifyJS 称之为 "resolution plan" 的依赖关系图 . `inRequestScope` 在一个对象的依赖关系图中将以单例形式出现. 者减少了需要进行解析的次数同时得到了一定的性能提升.
