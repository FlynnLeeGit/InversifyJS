# 对类的支持
InversifyJS 允许你的类对其他类进行直接依赖. 此时你必须使用 `@injectable` 注解进行声明， `@inject` 注解是非必须的 . 

当你使用一个类的时候 `@inject` 装饰器是非必须的. 因为 typescript 会为我们生成元数据. 然而以下情景不会自动生成:

- Import `reflect-metadata`
- Set `emitDecoratorMetadata` to `true` in `tsconfig.json`.

```ts
import { Container, injectable, inject } from "inversify";

@injectable()
class Katana {
    public hit() {
        return "cut!";
    }
}

@injectable()
class Shuriken {
    public throw() {
        return "hit!";
    }
}

@injectable()
class Ninja implements Ninja {

    private _katana: Katana;
    private _shuriken: Shuriken;

    public constructor(katana: Katana, shuriken: Shuriken) {
        this._katana = katana;
        this._shuriken = shuriken;
    }

    public fight() { return this._katana.hit(); };
    public sneak() { return this._shuriken.throw(); };

}

var container = new Container();
container.bind<Ninja>(Ninja).to(Ninja);
container.bind<Katana>(Katana).to(Katana);
container.bind<Shuriken>(Shuriken).to(Shuriken);
```

# 实例的自绑定

如果你需要解析一个已经存在的实例类型还需要进行绑定会显得特别冗余:

```ts
container.bind<Samurai>(Samurai).to(Samurai);
```

此时我们提供了一个 `toSelf` 方法来实现更好的操作:

```ts
container.bind<Samurai>(Samurai).toSelf();
```
