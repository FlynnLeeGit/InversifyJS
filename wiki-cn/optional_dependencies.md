# 可选依赖项

我们可以使用 `@optional()` 注解声明一个可选的依赖项:

```ts
@injectable()
class Katana {
    public name: string;
    public constructor() {
        this.name = "Katana";
    }
}

@injectable()
class Shuriken {
    public name: string;
    public constructor() {
        this.name = "Shuriken";
    }
}

@injectable()
class Ninja {
    public name: string;
    public katana: Katana;
    public shuriken: Shuriken;
    public constructor(
        @inject("Katana") katana: Katana,
        @inject("Shuriken") @optional() shuriken: Shuriken // Optional!
    ) {
        this.name = "Ninja";
        this.katana = katana;
        this.shuriken = shuriken;
    }
}

let container = new Container();

container.bind<Katana>("Katana").to(Katana);
container.bind<Ninja>("Ninja").to(Ninja);

let ninja =  container.get<Ninja>("Ninja");
expect(ninja.name).to.eql("Ninja");
expect(ninja.katana.name).to.eql("Katana");
expect(ninja.shuriken).to.eql(undefined);

container.bind<Shuriken>("Shuriken").to(Shuriken);

ninja =  container.get<Ninja>("Ninja");
expect(ninja.name).to.eql("Ninja");
expect(ninja.katana.name).to.eql("Katana");
expect(ninja.shuriken.name).to.eql("Shuriken");
```

在这个例子中，我们可以看到我们第一次是如何解析 `Ninja` 的, 因为我们没有对 `Shuriken` 的绑定项进行定义, 因此他的 `shuriken` 属性是未定义状态
并且我们给它加上了 `@optional()` 注解.

之后我们绑定了 `Shuriken`:

```ts
container.bind<Shuriken>("Shuriken").to(Shuriken);
```

所有已解析的 `Ninja` 实例都会被包含到 `Shuriken` 里面去.

## 默认值

如果一个依赖项被 `@optional()` 注解进行声明, 就像你在其他的 TypeScript 应用中做的一样,我们能定义一个默认值:

```ts
@injectable()
class Ninja {
    public name: string;
    public katana: Katana;
    public shuriken: Shuriken;
    public constructor(
        @inject("Katana") katana: Katana,
        @inject("Shuriken") @optional() shuriken: Shuriken = { name: "DefaultShuriken" } // Default value!
    ) {
        this.name = "Ninja";
        this.katana = katana;
        this.shuriken = shuriken;
    }
}
```