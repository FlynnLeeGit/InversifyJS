# 容器模块声明

容器模块可以帮助你在非常复杂的应用中对复杂的依赖关系进行管理.

```ts
let warriors = new ContainerModule((bind: interfaces.Bind, unbind: interfaces.Unbind) => {
    bind<Ninja>("Ninja").to(Ninja);
});

let weapons = new ContainerModule(
    (
        bind: interfaces.Bind,
        unbind: interfaces.Unbind,
        isBound: interfaces.IsBound,
        rebind: interfaces.Rebind
    ) => {
        bind<Katana>("Katana").to(Katana);
        bind<Shuriken>("Shuriken").to(Shuriken);
    }
);

let container = new Container();
container.load(warriors, weapons);
container.unload(warriors);
```
