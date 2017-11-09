# 常量或动态值的注入

将一个常量绑定到抽象接口:
```ts
container.bind<Katana>("Katana").toConstantValue(new Katana());
```
绑定一个动态值到抽象接口:
```ts
container.bind<Katana>("Katana").toDynamicValue((context: interfaces.Context) => { return new Katana(); });
```
