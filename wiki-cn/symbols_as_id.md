# 对 Symbols 的支持

在一个非常大的项目中使用字符串作为 InversifyJS 的类型标识符很容易造成命名冲突问题. 
InversifyJS 支持并且建议我们使用 [Symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) 来代替字符串作为名称标识符.

> symbol 是一种唯一且固定的数据类型,可以用作对象属性的标识符. symbol 对象的实质是自动帮我们在在原始数据外添加了一层对象包装.

```ts
import { Container, injectable, inject } from "inversify";

let Symbols = {
	Ninja : Symbol("Ninja"),
	Katana : Symbol("Katana"),
	Shuriken : Symbol("Shuriken")
};

@injectable()
class Katana implements Katana {
    public hit() {
        return "cut!";
    }
}

@injectable()
class Shuriken implements Shuriken {
    public throw() {
        return "hit!";
    }
}

@injectable()
class Ninja implements Ninja {

    private _katana: Katana;
    private _shuriken: Shuriken;

    public constructor(
	    @inject(Symbols.Katana) katana: Katana,
	    @inject(Symbols.Shuriken) shuriken: Shuriken
    ) {
        this._katana = katana;
        this._shuriken = shuriken;
    }

    public fight() { return this._katana.hit(); };
    public sneak() { return this._shuriken.throw(); };

}

var container = new Container();
container.bind<Ninja>(Symbols.Ninja).to(Ninja);
container.bind<Katana>(Symbols.Katana).to(Katana);
container.bind<Shuriken>(Symbols.Shuriken).to(Shuriken);
```
