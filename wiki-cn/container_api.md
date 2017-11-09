# 容器的 API 接口

InversifyJS 容器提供了一些针对多重注入和模糊绑定问题的帮助.

## 容器配置选项

### defaultScope(默认作用域)

指的默认情况下提供诸如对象的作用域形式, 一般默认提供的是 `临时` 对象, 但根据需要你也可以在绑定时候指定:

```ts
container.bind<Warrior>(TYPES.Warrior).to(Ninja).inSingletonScope();
container.bind<Warrior>(TYPES.Warrior).to(Ninja).inTransientScope();
```

你可以在应用级通过配置容器的构造函数初始参数来改变磨绒作用域:

```ts
let container = new Container({ defaultScope: "Singleton" });
```

### autoBindInjectable(注入对象的自动绑定)

你可以通过开启该配置项自动绑定被 `@injectable()` 注解装饰器所声明的类:

```ts
let container = new Container({ autoBindInjectable: true });
container.isBound(Ninja);          // returns false
container.get(Ninja);              // returns a Ninja
container.isBound(Ninja);          // returns true
```

手动绑定的优先级高于自动绑定:

```ts
let container = new Container({ autoBindInjectable: true });
container.bind(Ninja).to(Samurai);
container.get(Ninja);              // returns a Samurai
```

## Container.merge(a: Container, b: Container)

将两个容器合并成为一个容器:

```ts
@injectable()
class Ninja {
    public name = "Ninja";
}

@injectable()
class Shuriken {
    public name = "Shuriken";
}

let CHINA_EXPANSION_TYPES = {
    Ninja: "Ninja",
    Shuriken: "Shuriken"
};

let chinaExpansionContainer = new Container();
chinaExpansionContainer.bind<Ninja>(CHINA_EXPANSION_TYPES.Ninja).to(Ninja);
chinaExpansionContainer.bind<Shuriken>(CHINA_EXPANSION_TYPES.Shuriken).to(Shuriken);

@injectable()
class Samurai {
    public name = "Samurai";
}

@injectable()
class Katana {
    public name = "Katana";
}

let JAPAN_EXPANSION_TYPES = {
    Katana: "Katana",
    Samurai: "Samurai"
};

let japanExpansionContainer = new Container();
japanExpansionContainer.bind<Samurai>(JAPAN_EXPANSION_TYPES.Samurai).to(Samurai);
japanExpansionContainer.bind<Katana>(JAPAN_EXPANSION_TYPES.Katana).to(Katana);

let gameContainer = Container.merge(chinaExpansionContainer, japanExpansionContainer);
expect(gameContainer.get<Ninja>(CHINA_EXPANSION_TYPES.Ninja).name).to.eql("Ninja");
expect(gameContainer.get<Shuriken>(CHINA_EXPANSION_TYPES.Shuriken).name).to.eql("Shuriken");
expect(gameContainer.get<Samurai>(JAPAN_EXPANSION_TYPES.Samurai).name).to.eql("Samurai");
expect(gameContainer.get<Katana>(JAPAN_EXPANSION_TYPES.Katana).name).to.eql("Katana");
```

## container.getNamed<T>()

名称绑定:

```ts
let container = new Container();
container.bind<Weapon>("Weapon").to(Katana).whenTargetNamed("japonese");
container.bind<Weapon>("Weapon").to(Shuriken).whenTargetNamed("chinese");

let katana = container.getNamed<Weapon>("Weapon", "japonese");
let shuriken = container.getNamed<Weapon>("Weapon", "chinese");
```

## container.getTagged<T>()

标签绑定:

```ts
let container = new Container();
container.bind<Weapon>("Weapon").to(Katana).whenTargetTagged("faction", "samurai");
container.bind<Weapon>("Weapon").to(Shuriken).whenTargetTagged("faction", "ninja");

let katana = container.getTagged<Weapon>("Weapon", "faction", "samurai");
let shuriken = container.getTagged<Weapon>("Weapon", "faction", "ninja");
```

## container.getAll<T>()

获取给定标识符的所有可用绑定:

```ts
let container = new Container();
container.bind<Weapon>("Weapon").to(Katana);
container.bind<Weapon>("Weapon").to(Shuriken);

let weapons = container.getAll<Weapon>("Weapon");  // returns Weapon[]
```

## container.getAllNamed<T>()

获取给定标识符的所有有效绑定 
命名约束:

```ts
let container = new Container();

interface Intl {
    hello?: string;
    goodbye?: string;
}

container.bind<Intl>("Intl").toConstantValue({ hello: "bonjour" }).whenTargetNamed("fr");
container.bind<Intl>("Intl").toConstantValue({ goodbye: "au revoir" }).whenTargetNamed("fr");

container.bind<Intl>("Intl").toConstantValue({ hello: "hola" }).whenTargetNamed("es");
container.bind<Intl>("Intl").toConstantValue({ goodbye: "adios" }).whenTargetNamed("es");

let fr = container.getAllNamed<Intl>("Intl", "fr");
expect(fr.length).to.eql(2);
expect(fr[0].hello).to.eql("bonjour");
expect(fr[1].goodbye).to.eql("au revoir");

let es = container.getAllNamed<Intl>("Intl", "es");
expect(es.length).to.eql(2);
expect(es[0].hello).to.eql("hola");
expect(es[1].goodbye).to.eql("adios");
```


## container.getAllTagged<T>()

获取给定标识符的所有可用绑定，匹配给定的给定标识符
命名约束:

```ts
let container = new Container();

interface Intl {
    hello?: string;
    goodbye?: string;
}

container.bind<Intl>("Intl").toConstantValue({ hello: "bonjour" }).whenTargetTagged("lang", "fr");
container.bind<Intl>("Intl").toConstantValue({ goodbye: "au revoir" }).whenTargetTagged("lang", "fr");

container.bind<Intl>("Intl").toConstantValue({ hello: "hola" }).whenTargetTagged("lang", "es");
container.bind<Intl>("Intl").toConstantValue({ goodbye: "adios" }).whenTargetTagged("lang", "es");

let fr = container.getAllTagged<Intl>("Intl", "lang", "fr");
expect(fr.length).to.eql(2);
expect(fr[0].hello).to.eql("bonjour");
expect(fr[1].goodbye).to.eql("au revoir");

let es = container.getAllTagged<Intl>("Intl", "lang", "es");
expect(es.length).to.eql(2);
expect(es[0].hello).to.eql("hola");
expect(es[1].goodbye).to.eql("adios");
```

## container.isBound(serviceIdentifier: ServiceIdentifier)

如果你对标识符进行过绑定或者注册过服务提供者,可用 `isBound` 方法来进行检测.

```ts
interface Warrior {}
let warriorId = "Warrior";
let warriorSymbol = Symbol("Warrior");

@injectable()
class Ninja implements Warrior {}

interface Katana {}
let katanaId = "Katana";
let katanaSymbol = Symbol("Katana");

@injectable()
class Katana implements Katana {}

let container = new Container();
container.bind<Warrior>(Ninja).to(Ninja);
container.bind<Warrior>(warriorId).to(Ninja);
container.bind<Warrior>(warriorSymbol).to(Ninja);

container.isBound(Ninja)).eql(true);
container.isBound(warriorId)).eql(true);
container.isBound(warriorSymbol)).eql(true);
container.isBound(Katana)).eql(false);
container.isBound(katanaId)).eql(false);
container.isBound(katanaSymbol)).eql(false);
```

## container.isBoundNamed(serviceIdentifier: ServiceIdentifier<any>, named: string)

如果你可用 `isBoundNamed` 方法来进行检测给定的标识符是否进行过名称绑定.

```ts
const zero = "Zero";
const invalidDivisor = "InvalidDivisor";
const validDivisor = "ValidDivisor";
let container = new Container();

expect(container.isBound(zero)).to.eql(false);
container.bind<number>(zero).toConstantValue(0);
expect(container.isBound(zero)).to.eql(true);

container.unbindAll();
expect(container.isBound(zero)).to.eql(false);
container.bind<number>(zero).toConstantValue(0).whenTargetNamed(invalidDivisor);
expect(container.isBoundNamed(zero, invalidDivisor)).to.eql(true);
expect(container.isBoundNamed(zero, validDivisor)).to.eql(false);

container.bind<number>(zero).toConstantValue(1).whenTargetNamed(validDivisor);
expect(container.isBoundNamed(zero, invalidDivisor)).to.eql(true);
expect(container.isBoundNamed(zero, validDivisor)).to.eql(true);
```

## container.isBoundTagged(serviceIdentifier: ServiceIdentifier<any>, key: string, value: any)

如果你可用 `isBoundTagged` 方法来进行检测给定的标识符是否进行过标签绑定.

```ts
const zero = "Zero";
const isValidDivisor = "IsValidDivisor";
let container = new Container();

expect(container.isBound(zero)).to.eql(false);
container.bind<number>(zero).toConstantValue(0);
expect(container.isBound(zero)).to.eql(true);

container.unbindAll();
expect(container.isBound(zero)).to.eql(false);
container.bind<number>(zero).toConstantValue(0).whenTargetTagged(isValidDivisor, false);
expect(container.isBoundTagged(zero, isValidDivisor, false)).to.eql(true);
expect(container.isBoundTagged(zero, isValidDivisor, true)).to.eql(false);

container.bind<number>(zero).toConstantValue(1).whenTargetTagged(isValidDivisor, true);
expect(container.isBoundTagged(zero, isValidDivisor, false)).to.eql(true);
expect(container.isBoundTagged(zero, isValidDivisor, true)).to.eql(true);
```

## container.rebind<T>(serviceIdentifier: ServiceIdentifier<T>)

我们能够通过 `rebind` 方法去替换一个已经存在的绑定，该方法需要传入一个 `服务标志符（serviceIdentifier）`.
该函数返回一个 `BindingToSyntax` 实例，该实例可用作创建一个用于替换的绑定.

```ts
let TYPES = {
    someType: "someType"
};

let container = new Container();
container.bind<number>(TYPES.someType).toConstantValue(1);
container.bind<number>(TYPES.someType).toConstantValue(2);

let values1 = container.getAll(TYPES.someType);
expect(values1[0]).to.eq(1);
expect(values1[1]).to.eq(2);

container.rebind<number>(TYPES.someType).toConstantValue(3);
let values2 = container.getAll(TYPES.someType);
expect(values2[0]).to.eq(3);
expect(values2[1]).to.eq(undefined);
```

## container.resolve<T>(constructor: Newable<T>)
类似于 `container.get<T>(serviceIdentifier: ServiceIdentifier<T>)` 方法, 但是它允许用户在未绑定的情况下创建一个实例:

```ts
@injectable()
class Katana {
    public hit() {
        return "cut!";
    }
}

@injectable()
class Ninja implements Ninja {
    public katana: Katana;
    public constructor(katana: Katana) {
        this.katana = katana;
    }
    public fight() { return this.katana.hit(); }
}

const container = new Container();
container.bind(Katana).toSelf();

const tryGet = () => container.get(Ninja);
expect(tryGet).to.throw("No matching bindings found for serviceIdentifier: Ninja");

const ninja = container.resolve(Ninja);
expect(ninja.fight()).to.eql("cut!");
```

请注意它仅仅允许跳过依赖关系图中的根节点继续努力绑定 (composition root). 前面所以的依赖项 (e.g. `Katana` 在文章之前的例子中) 都必须对绑定进行声明.
