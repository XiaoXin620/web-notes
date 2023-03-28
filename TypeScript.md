# 基本类型


- 类型声明
   - 类型声明是TS非常重要的一个特点
   - 通过类型声明可以指定TS中变量（参数、形参）的类型
   - 指定类型后，当为变量赋值时，TS编译器会自动检查值是否符合类型声明，符合则赋值，否则报错
   - 简而言之，类型声明给变量设置了类型，使得变量只能存储某种类型的值
   - 语法：```jsx
let 变量: 类型;
let 变量: 类型 = 值;
function fn(参数: 类型, 参数: 类型): 类型{ ...
}
```

- 自动类型判断
   - TS拥有自动的类型判断机制
   - 当对变量的声明和赋值是同时进行的，TS编译器会自动判断变量的类型
   - 所以如果你的变量的声明和赋值时同时进行的，可以省略掉类型声明
- 规定参数类型意义
   - 当执行一些不合理的操作时，将提前提醒你错误
   - 比如```tsx
const a = 'xxxx'; 
a.map(); //那么将报错，因为ts已经认定a为字符串类型而非数组;
```




[类型](https://www.notion.so/f70ece4dcfd94600951720b5cf70efa9)


- number```typescript
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: bigint = 100n;
```

- string```typescript
let color: string = "blue";
color = 'red';

let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${fullName}.

I'll be ${age + 1} years old next month.`;
```

- boolean```typescript
let isDone: boolean = false;
```

- object(用得少)
   - 无需设定默认数据类型，ts会根据初始值自己判定
   - 注意对象类型标注内容类型，并且";"进行分割
```typescript
let obj: object = { name:'tom'}; //正常使用
const xx : { name: string; age: number } = { name: 'zt', age: 19 }; //设定数据类型:
```

- array
   - 数组的混合存储: 正常情况下，无需设定默认数据类型，ts会根据初始值自己判定
```typescript
let list: number[] = [1, 2, 3];
let list1: Array<number> = [1, 2, 3]; // 数组只能存储数字类型
let list2: [number,string] = [1,'test'];// 对应参数类型: 只能这么存储，不符合将报错
let list3: (string|number)[] = [1,'2',123,'xxx']; // 多种类型混合存储: 只允许数字以及字符串类型存
```

- tuple```typescript
let x: [string, number];
x = ["hello", 10];
```

- enum
   - 存储: 数值类型，字符串类型, 其它类型都不支持
   - 其存储内容为只读，不可修改
   - 用途: 用于代替，多个const创建变量, 方便调用，要求数据只读不可修改
```typescript
enum Color {       //自动枚举索引值，从0开始
  Red,              
  Green,
  Blue,
}
let c: Color = Color.Green;

enum Color {
  Red = 1,
  Green,
  Blue,
}
let c: Color = Color.Green;

enum Color {
  Red = 1,
  Green = 2,
  Blue = 4,
}
let c: Color = Color.Green;

enum Color {  //自动枚举索引值，从3开始
  Red = 3,
  Green,
  Blue,
}
```

- any（尽量不用）```typescript
let d: any = 4;
d = 'hello';
d = true;
```

- void（没有实际返回值使用或者返回 undefined）```typescript
function result(num1:number,num2:number): void {  // 这里如果使用undefined 则一定要写return语句 
	consolog.log(num1+num2)                         // void就不需要
}

function addAndHandle(n1: number, n2: number, cb: (num: number) => void) { // cb 回调函数参数和返回类型
  const result = n1 + n2;
  cb(result);
}
```

- unknown （用any 不如用这个）
   - 报错: 虽然可以保存各种类型的数据，但是无法赋值给其它非any类型的变量
   - 避免报错: 做类型判断
```typescript
let userInput: unknown;
let userName: string;

// 类型判断: 做类型判断将不在报错
let userInput: unknown;
let userName: string;

userInput = 5;
userInput = 'Max';
if (typeof userInput === 'string') {
  userName = userInput;
}
```

- never```typescript
function generateError(message: string, code: number): never { //永远没有返回值 一般用来报错
  throw { message: message, errorCode: code };
  // while (true) {}
}

generateError('An error occurred!', 500);
```

- type 类型别名
   - 通常用于，方便放入任意规定数据类型的位置;
- 联合类型
- 字符串字面量类型```typescript
type TypeNS = number | string;             // type类型别名: 通常用于，方便放入任意规定数据类型的位置;
type TypeIsNS = 'is-number' | 'is-string';

const conbin = ( 
    input0: number | string,                // 联合类型: 只接受number/string类型数据，否则将报错
    input1: TypeNS,
    countType: 'is-number' | 'is-string'    // 字符串字面量类型: 只接受'is-number'/'is-string'字符串类型数据，否则将报错
) => {
    if( countType === 'is-number' ){
        return +input0 + +input1;
    }
    else if ( countType === 'is-string' ){
        return input0.toString() + input1.toString();
    }
    else{
        return null;
    }
}

console.log(conbin( '100', '50', 'is-number' ));
```

- 函数返回数据类型设定
   - 设定函数return指定类型结果
   - void类型函数
   - undefind类型函数
   - never类型函数: 保证函数不返回任何数据,常用于throw主动造成错误的函数
```typescript
// 设定函数返回number类型
// 设定函数return指定类型结果
 const testDIY = ( input: number ): number => {             
    return input;
 }

// 函数返回void类型
    //注意: 如果函数中无return语句，则ts认定函数为返回void
 const testVoid = ( input: number ): void => {
     console.log( input );
 }

// 无return，ts认定为返回void的函数
 const testVoid_normal = ( input: number ) =>{              
     console.log(input);
 }

// 函数返回undefind类型
    //注意: 如果函数有return但无返回值，则ts认定函数返回undefind
 const testUndefind = ( input: number ): undefined => {
     return;
 }
```


# 编译选项


- 自动编译文件
   - 编译文件时，使用 -w 指令后，TS编译器会自动监视文件的变化，并在文件发生变化时对文件进行重新编译。
   - 示例：
      - `tsc xxx.ts -w`
- 自动编译整个项目
   - 如果直接使用tsc指令，则可以自动将当前项目下的所有ts文件编译为js文件。
   - 但是能直接使用tsc命令的前提时，要先在项目根目录下创建一个ts的配置文件 tsconfig.json
   - tsconfig.json是一个JSON文件，添加配置文件后，只需只需 tsc 命令即可完成对整个项目的编译
   - 配置选项：
      - include
         - 定义希望被编译文件所在的目录
         - 默认值：["**/*"]
         - 示例：```
"include":["src/**/*", "tests/**/*"]
```

            - 上述示例中，所有src目录和tests目录下的文件都会被编译
      - exclude
         - 定义需要排除在外的目录
         - 默认值：["node_modules", "bower_components", "jspm_packages"]
         - 示例：```javascript
"exclude": ["./src/hello/**/*"]
```

            - 上述示例中，src下hello目录下的文件都不会被编译
      - extends
         - 定义被继承的配置文件
         - 示例：```javascript
"extends": "./configs/base"
```

            - 上述示例中，当前配置文件中会自动包含config目录下base.json中的所有配置信息
      - files
         - 指定被编译文件的列表，只有需要编译的文件少时才会用到
         - 示例：```javascript
"files": [ "core.ts", "sys.ts", "types.ts", "scanner.ts", "parser.ts", "utilities.ts", "binder.ts", "checker.ts", "tsc.ts" ]
```

            - 列表中的文件都会被TS编译器所编译
         - compilerOptions
            - 编译选项是配置文件中非常重要也比较复杂的配置选项
            - 在compilerOptions中包含多个子选项，用来完成对编译的配置
               - 项目选项
                  - target
                     - 设置ts代码编译的目标版本
                     - 可选值：
                        - ES3（默认）、ES5、ES6/ES2015、ES7/ES2016、ES2017、ES2018、ES2019、ES2020、ESNext
                     - 示例：```javascript
"compilerOptions": { "target": "ES6"}
```

                        - 如上设置，我们所编写的ts代码将会被编译为ES6版本的js代码
                  - lib
                     - 指定代码运行时所包含的库（宿主环境）
                     - 可选值：
                        - ES5、ES6/ES2015、ES7/ES2016、ES2017、ES2018、ES2019、ES2020、ESNext、DOM、WebWorker、ScriptHost ......
                     - 示例：```javascript
"compilerOptions": { "target": "ES6", "lib": ["ES6", "DOM"], "outDir": "dist", "outFile": "dist/aa.js"}
```

                  - module
                     - 设置编译后代码使用的模块化系统
                     - 可选值：
                        - CommonJS、UMD、AMD、System、ES2020、ESNext、None
                     - 示例：```javascript
"compilerOptions": { "module": "CommonJS"}
```

                  - outDir
                     - 编译后文件的所在目录
                     - 默认情况下，编译后的js文件会和ts文件位于相同的目录，设置outDir后可以改变编译后文件的位置
                     - 示例：```javascript
"compilerOptions": { "outDir": "dist"}
```

                        - 设置后编译后的js文件将会生成到dist目录
                  - outFile
                     - 将所有的文件编译为一个js文件
                     - 默认会将所有的编写在全局作用域中的代码合并为一个js文件，如果module制定了None、System或AMD则会将模块一起合并到文件之中
                     - 示例：```javascript
"compilerOptions": { "outFile": "dist/app.js"}
```

                  - rootDir
                     - 指定代码的根目录，默认情况下编译后文件的目录结构会以最长的公共目录为根目录，通过rootDir可以手动指定根目录
                     - 示例：```javascript
"compilerOptions": { "rootDir": "./src"}
```

                  - allowJs
                     - 是否对js文件编译
                  - checkJs
                     - 是否对js文件进行检查
                     - 示例：```javascript
"compilerOptions": { "allowJs": true, "checkJs": true}
```

                  - removeComments
                     - 是否删除注释
                     - 默认值：false
                  - noEmit
                     - 不对代码进行编译
                     - 默认值：false
                  - sourceMap
                     - 是否生成sourceMap
                     - 默认值：false
               - 严格检查
                  - strict
                     - 启用所有的严格检查，默认值为true，设置后相当于开启了所有的严格检查
                  - alwaysStrict
                     - 总是以严格模式对代码进行编译
                  - noImplicitAny
                     - 禁止隐式的any类型
                  - noImplicitThis
                     - 禁止类型不明确的this
                  - strictBindCallApply
                     - 严格检查bind、call和apply的参数列表
                  - strictFunctionTypes
                     - 严格检查函数的类型
                  - strictNullChecks
                     - 严格的空值检查
                  - strictPropertyInitialization
                     - 严格检查属性是否初始化
               - 额外检查
                  - noFallthroughCasesInSwitch
                     - 检查switch语句包含正确的break
                  - noImplicitReturns
                     - 检查函数没有隐式的返回值
                  - noUnusedLocals
                     - 检查未使用的局部变量
                  - noUnusedParameters
                     - 检查未使用的参数
               - 高级
                  - allowUnreachableCode
                     - 检查不可达代码
                     - 可选值：
                        - true，忽略不可达代码
                        - false，不可达代码将引起错误
                  - noEmitOnError
                     - 有错误的情况下不进行编译
                     - 默认值：false



## tsconfig.js文件


```javascript
"compilerOptions": {
    /* Basic Options */
    // "incremental": true,                   /* Enable incremental compilation */
    "target": "es5",                          /* 决定ts编译的js版本 */
    "module": "commonjs",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'. */
    // "lib": [],                             /* 引用函数库,建议默认 | Specify library files to be included in the compilation. */
    // "allowJs": true,                       /* Allow javascript files to be compiled. */
    // "checkJs": true,                       /* 不仅检测ts文件错误也检测js文件错误 | Report errors in .js files. */
    // "jsx": "preserve",                     /* 给React使用的属性( 等待补充 ) */
    // "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    // "declarationMap": true,                /* Generates a sourcemap for each corresponding '.d.ts' file. */
    "sourceMap": true,                        /* 可以在浏览器中调试ts文件，此乃必备属性 | Generates corresponding '.map' file. */
    // "outFile": "./",                       /* Concatenate and emit output to single file. */
    "outDir": "./dist",                       /* 配置编译输出目录 | Redirect output structure to the directory. */
    "rootDir": "./src",                       /* 配置监听编译根目录 */
    // "composite": true,                     /* Enable project compilation */
    // "tsBuildInfoFile": "./",               /* Specify file to store incremental compilation information */
    // "removeComments": true,                /* 编译后无注释文字 | Do not emit comments to output. */
    // "noEmit": true,                        /* ts文件不进行编译 | Do not emit outputs. */
    // "importHelpers": true,                 /* Import emit helpers from 'tslib'. */
    // "downlevelIteration": true,            /* 急救编译，如果ts文件编译不正常可开启此选项 */
    // "isolatedModules": true,               /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */
    "noEmitOnError": false,                   /* true时如果ts文件报错将不生成js文件 | Do not emit outputs. */

    /*  错误检测配置 | Strict Type-Checking Options */
    "strict": true,                           /* Enable all strict type-checking options. */
    // "noImplicitAny": true,                 /* 变量不定义初始化类型也不会报错 */
    // "strictNullChecks": true,              /* 启用严格的null检查，默认为开启，如果关闭可以省略"!"符号，不建议关闭 | Enable strict null checks. */
    // "strictFunctionTypes": true,           /* 严格的函数检测，默认为开启 | Enable strict checking of function types. */
    // "strictBindCallApply": true,           /* Enable strict 'bind', 'call', and 'apply' methods on functions. */
    // "strictPropertyInitialization": true,  /* Enable strict checking of property initialization in classes. */
    // "noImplicitThis": true,                /* Raise error on 'this' expressions with an implied 'any' type. */
    // "alwaysStrict": true,                  /* Parse in strict mode and emit "use strict" for each source file. */

    /* Additional Checks */
    // "noUnusedLocals": true,                /* 若const变量没有被使用则报错 | Report errors on unused locals. */
    // "noUnusedParameters": false,           /* 若函数中的参数未使用则报错,建议关闭 | Report errors on unused parameters. */
    // "noImplicitReturns": true,             /* 函数必须有return语句，否则将报错 */
    // "noFallthroughCasesInSwitch": true,    /* 在switch语句中报告失败情况的错误 | Report errors for fallthrough cases in switch statement. */

    /* Module Resolution Options */
    // "moduleResolution": "node",            /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    // "baseUrl": "./",                       /* Base directory to resolve non-absolute module names. */
    // "paths": {},                           /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    // "rootDirs": [],                        /* List of root folders whose combined content represents the structure of the project at runtime. */
    // "typeRoots": [],                       /* List of folders to include type definitions from. */
    // "types": [],                           /* Type declaration files to be included in compilation. */
    // "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
    "esModuleInterop": true,                  /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
    // "preserveSymlinks": true,              /* Do not resolve the real path of symlinks. */
    // "allowUmdGlobalAccess": true,          /* Allow accessing UMD globals from modules. */

    /* Source Map Options */
    // "sourceRoot": "",                      /* Specify the location where debugger should locate TypeScript files instead of source locations. */
    // "mapRoot": "",                         /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,               /* Emit a single file with source maps instead of having a separate file. */
    // "inlineSources": true,                 /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */

    /* Experimental Options */
    // "experimentalDecorators": true,        /* 启用对ES7装饰器的实验性支持 | Enables experimental support for ES7 decorators. */
    // "emitDecoratorMetadata": true,         /* 启用实验性地支持为装饰者发送类型元数据。| Enables experimental support for emitting type metadata for decorators. */

    /* Advanced Options */
    "forceConsistentCasingInFileNames": true  /* 禁止对同一文件使用大小写不一致的引用( 机翻 ) | Disallow inconsistently-cased references to the same file. */
  },
  // "include": []                            /* 指定编译目标，默认开启全局监听，不建议手动调整 */
  "exclude": [                                /* 排除编译目标 */
    "node_modules",                           // 默认必须有
    "*.dev.ts",                               // 不编译当前目录下的"*.dev.ts"
    "**/*.dev.ts"                             // 不编译文件目录内的"*.dev.ts"
  ]
}
```


# 类和接口（Classes & Interfaces）


## 类（Classes）


- 定义类：```typescript
class 类名 {
	属性名: 类型;
	
	constructor(参数: 类型){
		this.属性名 = 参数;
	}
	
	方法名(){
		....
	}

}
```

- 示例：```typescript
class Person{
    name: string;
    age: number;

    constructor(name: string, age: number){
        this.name = name;
        this.age = age;
    }

    sayHello(){
        console.log(`大家好，我是${this.name}`);
    }
}
```

- 使用类：```typescript
const p = new Person('孙悟空', 18);
p.sayHello();
```




### 面向对象的特点


- 封装
   - 对象实质上就是属性和方法的容器，它的主要作用就是存储属性和方法，这就是所谓的封装
   - 默认情况下，对象的属性是可以任意的修改的，为了确保数据的安全性，在TS中可以对属性的权限进行设置
   - 只读属性（readonly）：
      - 如果在声明属性时添加一个readonly，则属性便成了只读属性无法修改
   - TS中属性具有三种修饰符：
      - public（默认值），可以在类、子类和对象中修改
      - protected ，可以在类、子类中修改
      - private ，可以在类中修改
   - 示例：
      - public```typescript
class Person{
    public name: string; // 写或什么都不写都是public
    public age: number;

    constructor(name: string, age: number){
        this.name = name; // 可以在类中修改
        this.age = age;
    }

    sayHello(){
        console.log(`大家好，我是${this.name}`);
    }
}

class Employee extends Person{
    constructor(name: string, age: number){
        super(name, age);
        this.name = name; //子类中可以修改
    }
}

const p = new Person('孙悟空', 18);
p.name = '猪八戒';// 可以通过对象修改
```

      - protected```typescript
class Person{
    protected name: string;
    protected age: number;

    constructor(name: string, age: number){
        this.name = name; // 可以修改
        this.age = age;
    }

    sayHello(){
        console.log(`大家好，我是${this.name}`);
    }
}

class Employee extends Person{

    constructor(name: string, age: number){
        super(name, age);
        this.name = name; //子类中可以修改
    }
}

const p = new Person('孙悟空', 18);
p.name = '猪八戒';// 不能修改
```

      - private```typescript
class Person{
    private name: string;
    private age: number;

    constructor(name: string, age: number){
        this.name = name; // 可以修改
        this.age = age;
    }

    sayHello(){
        console.log(`大家好，我是${this.name}`);
    }
}

class Employee extends Person{

    constructor(name: string, age: number){
        super(name, age);
        this.name = name; //子类中不能修改
    }
}

const p = new Person('孙悟空', 18);
p.name = '猪八戒';// 不能修改
```

   - 属性存取器
      - 对于一些不希望被任意修改的属性，可以将其设置为private
      - 直接将其设置为private将导致无法再通过对象修改其中的属性
      - 我们可以在类中定义一组读取、设置属性的方法，这种对属性读取或设置的属性被称为属性的存取器
      - 读取属性的方法叫做setter方法，设置属性的方法叫做getter方法
      - 示例：```typescript
class Person{
    private _name: string;

    constructor(name: string){
        this._name = name;
    }

    get name(){
        return this._name;
    }

    set name(name: string){
        this._name = name;
    }

}

const p1 = new Person('孙悟空');
console.log(p1.name); // 通过getter读取name属性
p1.name = '猪八戒'; // 通过setter修改name属性
```

   - 静态属性
      - 静态属性（方法），也称为类属性。使用静态属性无需创建实例，通过类即可直接使用
      - 静态属性（方法）使用static开头
      - 示例：```typescript
class Tools{
    static PI = 3.1415926;
    
    static sum(num1: number, num2: number){
        return num1 + num2
    }
}

console.log(Tools.PI);
console.log(Tools.sum(123, 456));
```

   - this
      - 在类中，使用this表示当前对象
- 继承
   - 继承时面向对象中的又一个特性
   - 通过继承可以将其他类中的属性和方法引入到当前类中
      - 示例：```typescript
class Animal{
    name: string;
    age: number;

    constructor(name: string, age: number){
        this.name = name;
        this.age = age;
    }
}

class Dog extends Animal{

    bark(){
        console.log(`${this.name}在汪汪叫！`);
    }
}

const dog = new Dog('旺财', 4);
dog.bark();
```

   - 通过继承可以在不修改类的情况下完成对类的扩展
   - 重写
      - 发生继承时，如果子类中的方法会替换掉父类中的同名方法，这就称为方法的重写
      - 示例：```typescript
class Animal{
    name: string;
    age: number;

    constructor(name: string, age: number){
        this.name = name;
        this.age = age;
    }

    run(){
        console.log(`父类中的run方法！`);
    }
}

class Dog extends Animal{

    bark(){
        console.log(`${this.name}在汪汪叫！`);
    }

    run(){
        console.log(`子类中的run方法，会重写父类中的run方法！`);
    }
}

const dog = new Dog('旺财', 4);
dog.bark();
```

      - 在子类中可以使用super来完成对父类的引用
- 抽象类（abstract class）
   - 抽象类是专门用来被其他类所继承的类，它只能被其他类所继承不能用来创建实例```typescript
abstract class Animal{
    abstract run(): void;
    bark(){
        console.log('动物在叫~');
    }
}

class Dog extends Animals{
    run(){
        console.log('狗在跑~');
    }
}
```

   - 使用abstract开头的方法叫做抽象方法，抽象方法没有方法体只能定义在抽象类中，继承抽象类时抽象方法必须要实现
- 单例模式
   - 传统的单例模式可以用来解决所有代码必须写到 class 中的问题：```typescript
class Singleton {
  private static instance: Singleton;
  private constructor() {
    // ..
  }

  public static getInstance() {
    if (!Singleton.instance) {
      Singleton.instance = new Singleton();
    }

    return Singleton.instance;
  }

  someMethod() {}
}

let someThing = new Singleton(); // Error: constructor of 'singleton' is private

let instacne = Singleton.getInstance(); // do some thing with the instance
```




## 接口（Interface）


接口的作用类似于抽象类，不同点在于接口中的所有方法和属性都是没有实值的，换句话说接口中的所有方法都是抽象方法。接口主要负责定义一个类的结构，接口可以去限制一个对象的接口，对象只有包含接口中定义的所有属性和方法时才能匹配接口。同时，可以让一个类去实现接口，实现接口时类中要保护接口中的所有属性。


- implements使class可以使用interface对象模板
- 目的：定义好对象结构，然后将其用于变量设定为类型, 增加程序的可读性
- 注意：
   - interface只能使用于ts中
   - 并且interface变量开头大写
   - 类似于public,private等是无法使用的, readonly是可以使用的
- 使用：
   - let/const: 可以使用到普通变量
   - class: 可以通过implements使用interface模板对象
- 扩展接口: interface支持extends继承创建
- 示例（检查对象类型）：```typescript
interface Person{
    name: string;
    sayHello():void;
}

function fn(per: Person){
    per.sayHello();
}

fn({name:'孙悟空', sayHello() {console.log(`Hello, 我是 ${this.name}`)}});
```

- 示例（实现）```typescript
interface Person{
    name: string;
    sayHello():void;
}

class Student implements Person{
    constructor(public name: string) {
    }

    sayHello() {
        console.log('大家好，我是'+this.name);
    }
}
```




# 高级用法


## TYPE: “&"合并符号


- type为对象时，相当于二者对象要求合并```typescript
type TestA = {
    name: string,
    age: number,
}
type TestB = {
    name: string,
    addr: string,
    time: Date
}
type TestAnd = TestA & TestB;
const testInput: TestAnd = {
    name: 'zt',
    age: 19,
    addr: 'xxx',
    time: new Date()
}
console.log(testInput);
```

- type为单个变量时, 相当于二者，交集```typescript
type TestC = string | number;
type TestD = string | boolean;
type TestAndCD = TestC & TestD; // 相当于交集: type TestAndCD = string;
const TestInputCD: TestAndCD = '交集';
```




## interface 多个合并方式


```typescript
interface InterfaceTestA {
    name: string,
    age: number,
    time: Date,
}
interface InterfaceTestB{
    addr: string
}
interface InterfaceAnd extends InterfaceTestA, InterfaceTestB{}; // 多个interface合并核心
const interfaceInput: InterfaceAnd = {
    name: '007',
    age: 19,
    time: new Date,
    addr: 'xxxxxx'
}
```


## "|" 符号的使用


- 在函数中，可以通过判断其类型进行对应类型赋值。如果没有判断类型则将无法正常使用```typescript
interface bired {
    type: 'bired',
    flaySpeed: number
}

interface car {
    type: 'car',
    runSpeed: number
}

type orTest = bired | car;
const functionOrTest = ( props: orTest ) => {
    let speed: number;
    switch( props.type ){                       // 函数中，必须要有类型判断，否则将无法正常使用
        case "bired":
            speed = props.flaySpeed;
            break;
        case "car":
            speed = props.runSpeed;
            break;
    }
    console.log(`${props.type} - SPEED: ${speed}`);
};

functionOrTest( { type: 'car', runSpeed: 666 } );
```




## DOCUMENT正确的使用方法，防止报错


## AS类型转换,非常重要且常用


- 目的: 声明doucument抓取标签的类型，防止ts报错
   - 方法一: 
   - 方法二: as HTMLInputElement, "as"告诉ts此乃document抓取的标签类型
```typescript
// 方法一:
const buttonA = <HTMLInputElement>document.querySelector("#testInput")!;
buttonA.value = 'xxx';

// 方法二: "as"告诉ts此乃document抓取的标签类型, 否则将报错
const buttonB = document.querySelector("#testInput")! as HTMLInputElement;
buttonB.value = 'yyy';
```


## interface不确定属性名称构建


- [xxx:string]可以是任意属性名称```typescript
interface testNameBuild {
    [propsName: string]: string,    // 核心,与ES6使用方法相同{ [xxx]: yyy }
}

const testName: testNameBuild = {
    email: 'xxx@gmail.com',
    addr: 'xxxxx',
}
```




## 函数重载


- 告诉ts什么样的赋值给什么样的类型结果



```typescript
function testLoadFunction ( a: string, b: string ): string;             // 函数重载
function testLoadFunction ( a: number, b: number ): number;             // 函数重载
function testLoadFunction ( a: number | string, b: number | string ) {  // 原始函数
    if( typeof a === 'string' || typeof b === 'string' ){
        return a.toString() + b.toString();
    }
    return a+b;
}
const result = testLoadFunction( 'ztaetr','xxx' );
result.split('r')  // 报错: 如果没有"函数重载"，那么将报错
```


## "?"可选符号


- 函数中: 告诉ts为可选参数
- 索引变量中: 告诉ts我也不知是否能索引成功
- 目的: 告诉ts不要因为不确定变量来报错```typescript
const axiosUserDate = {
    id: 'xxx',
    name: 'zt',
    // job: { title: 'ceo', des: 'testxxx' }
}
console.log(axiosUserDate?.job?.title)// "?": 保证索引数据不存在js也能正常运行，并返回undefind，但ts会提示错误
```




## "??"双重可选符，“||”也可以做到其功能，只是“特殊点”不适合


- 目的: 给变量备用数据
- xx ?? bb: 如果xx数据为真，则返回真实数据，否则返回bb准备好的数据;
- 特殊点: ''空字符串也会被??视为真```typescript
const testInputDate = '';
const getDate = testInputDate ?? '备用数据';   // 备用数据
console.log(
   getDate
);
```




# 泛型（Generic）


定义一个函数或类时，有些情况下无法确定其中要使用的具体类型（返回值、参数、属性的类型不能确定），此时泛型便能够发挥作用。


- 举个例子：```typescript
function test(arg: any): any{
	return arg;
}
```

- 上例中，test函数有一个参数类型不确定，但是能确定的时其返回值的类型和参数的类型是相同的，由于类型不确定所以参数和返回值均使用了any，但是很明显这样做是不合适的，首先使用any会关闭TS的类型检查，其次这样设置也不能体现出参数和返回值是相同的类型
- 内置通用类型
   - 数组类型: Array<类型>```typescript
const names: Array<string> = [];
```

- 使用泛型：```typescript
function test<T>(arg: T): T{
	return arg;
}
```

- 这里的就是泛型，T是我们给这个类型起的名字（不一定非叫T），设置泛型后即可在函数中使用T来表示该类型。所以泛型其实很好理解，就表示某个类型。
- 那么如何使用上边的函数呢？
   - 方式一（直接使用）：```typescript
test(10)
```

      - 使用时可以直接传递参数使用，类型会由TS自动推断出来，但有时编译器无法自动推断时还需要使用下面的方式
   - 方式二（指定类型）：```typescript
test<number>(10)
```

      - 也可以在函数后手动指定泛型
   - 可以同时指定多个泛型，泛型间使用逗号隔开```typescript
function test<T, K>(a: T, b: K): K{
    return b;
}

test<number, string>(10, "hello");
```

      - 使用泛型时，完全可以将泛型当成是一个普通的类去使用
   - 类中同样可以使用泛型：```typescript
class MyClass<T>{
    prop: T;

    constructor(prop: T){
        this.prop = prop;
    }
}
```

   - 除此之外，也可以对泛型的范围进行约束```typescript
interface MyInter{
    length: number;
}

function test<T extends MyInter>(arg: T): number{
    return arg.length;
}
```

      - 使用T extends MyInter表示泛型T必须是MyInter的子类，不一定非要使用接口类和抽象类同样适用。
   - Partial
      - 参数类型变为可选
      - 要求输出结果xxx时：告诉ts，目标将成为xxx类型，请先不要报错
      - 无要求输出结果，则无需as转换```typescript
interface DateGoal {
    name: string,
    age: number,
    addr: string
}

// 注意: 要求输出结果为DateGoal类型
function testPartial ( name: string, age: number, addr: string ): DateGoal {
    let result: Partial<DateGoal> = {};     // Partial< xxxx >: 告诉ts，目标将成为xxx类型，请先不要报错
    result.name = name;
    result.age = age;
    result.addr = addr;

    return result as DateGoal;              // 输出数据时，如果报错，则使用“as转换其要求输出的类型”
}
```

      - Readonly
         - 只读类型的数据，无法修改
```typescript
const inputReadonly: Readonly<string[]> = [ 'zt', '19' ];
```

      - "通用类型" 与 "联合类型" 区别
         - 通用类型：在class中或者函数中，一开始就抉择数据类型
         - extends - 约束通用类型的目的:  约束允许您缩小可能在通用函数等中使用的具体类型。
         - 联合类型: 在class中或者函数中，一开始不能统一抉择数据类型
         - 通用类型更加灵活，如上方的 const testNewControlArray_string = new TestControlArray(), 如果只使用联合类型是很难实现这一步的。
         - 何时使用通用类型: 如果您拥有一个实际上可以与其他多种可能的类型一起使用的类型（例如，发出不同类型数据的对象）- 官方语。



# 装饰器（DECORATORS）


- 记录class代码
   - constructor: Function: 此参数代表，抓取当前文件中的class代码
   - 注意: constructor参数名称可随意修改
```typescript
function Logger( logMsg: string ){
    console.log('--- Logger Start ---');
    return function( constructor: Function ){
        console.log('--- Logger Function ---');
        console.log( logMsg );
        console.log( constructor );
    }
}
// 1. 通过装饰器，渲染H5标签
function WithTemplate( template: string , hookId: string ){
    console.log('--- WithTemplate Start ---');
    return function( constructor: any ){                            // 这里必须要传值，否则将报错
        console.log('--- WithTemplate Function ---');
        const hookTarget = document.getElementById(hookId)!;
        if( hookTarget ){
            const p = new constructor();                          // 实列化后，可以获取class中的属性
            hookTarget.innerHTML = template;
            hookTarget.querySelector('h1')!.textContent = p.name;  // 将class中name属性渲染到h1标签中
        }
    }
}
```

- 装饰器运行顺序：
   - 先从, 自上到下执行装饰器: Logger Start --> WithTemplate Start
   - 在从装饰器内部return函数, 自下而上执行: WithTemplate Function --> Logger Function
   - 网上查询：
      - 类装饰器——优先级 4
      - 访问器或属性装饰器——优先级 3
      - 方法装饰器——优先级 2
      - 参数装饰器——优先级 1
```typescript
@Logger( '测试装饰器，抓取class' )                                    // 运行装饰器 - 抓取class代码
@WithTemplate( `<h1>渲染个标题吧</h1>`, "app-test" )                  // 通过装饰器 - 渲染标签
class App {
    name = "Ztaer";
    constructor(){
        console.log("---- App - Start ----");
    }
}
const test = new App();
```

- 监听抓取方式
   - 装饰器监听方式: [@xxx ](/xxx ) yyyy; 
   - 参数:
      - 装饰器监听类型: class中的函数, class中的set函数，class中的变量： ( target: any, name: string | Symbol, des?: PropertyDescriptor )
      - 装饰器监听类型: 函数中的参数：( target: any, name: string | Symbol, position?:number )
```typescript
// 监听class中的变量
function Logger0( target: any, name: string | Symbol, des?: PropertyDescriptor ){
    console.log('Logger - 0 ------');
    console.log('target:',target);
    console.log('name:',name);
    console.log('des:',des);
}

// 监听class中的set
function Logger1( target: any, name: string | Symbol, des?: PropertyDescriptor ){
    console.log('Logger - 1 ------');
    console.log('target:',target);
    console.log('name:',name);
    console.log('des:',des);
}

// 监听函数
function Logger2( target: any, name: string | Symbol, des?: PropertyDescriptor ){
    console.log('Logger - 2 ------');
    console.log('target:',target);
    console.log('name:',name);
    console.log('des:',des);
}

// 监听函数中的参数
function Logger3( target: any, name: string | Symbol, position?:number ){
    console.log('Logger - 3 ------');
    console.log('target:',target);
    console.log('name:',name);
    console.log('popsition:',position);
}

class Product {
    @Logger0 titles: string;

    private _price: number;

    @Logger1 set price( val: number ){
        if( val > 0 ){
            this._price = val;
        }
    }

    constructor( t: string, @Logger3 p: number ){
        this.titles = t;
        this._price = p;
        console.log('开始');
    }

    
    @Logger2 getPrice(  tax: number ){
        return this._price * ( 1 + tax );
    }

}
```

- 主动BIND，对象中的函数，调用时无需BIND
   - 注意：其实按照ES6习惯的正常写法，默认就无需bind，但是ES5习惯的写法需要写bind
   - 正常写法: button.addEventListener('click', ()=>p.getDate );
   - 无装饰器bind写法：button.addEventListener('click', p.getDate.bind(p) );
   - 有装饰器，避免使用bind的写法: button.addEventListener('click', p.getDate );
```typescript
function AutoBind( _target: any, _name: string, descriptor: PropertyDescriptor ){
    const originMethod = descriptor.value;          // 在descriptor的value属性中为监听目标( 这里是getDate()函数 )
    const adjDescriptor: PropertyDescriptor = {    // 返回的descriptor内容
        configurable: true,   // 是否允许配置
        enumerable: false,    // 是否在循环中显示( 具体不清楚 )
        get(){                // get属性：方便用户执行额外的逻辑( 核心 )
            const boundFunction = originMethod.bind(this);  // 给getDate()函数提前bind，这样在使用时无需bind
            return boundFunction;
        }
    }
    return adjDescriptor;
}

class Printer {
    name:string = 'init data';
    @AutoBind getDate(){
        console.log( this.name );
    }
}

const p = new Printer();

const button = document.querySelector('button')!;
// 正常写法: button.addEventListener('click', ()=>p.getDate );
// 无装饰器bind写法：button.addEventListener('click', p.getDate.bind(p) );  
button.addEventListener('click', p.getDate ); // 有装饰器，避免使用bind的写法
```

- 实战表单数据验证
   - 验证思路:
      1. 构建数据结构：当前数据正常为{ Carouse:{ title: "required", price: "positive" } }
      2. 装饰器: 验证变量数值是否正常，若正常则将‘reduired’/'positive'根据数据结构加入变量中
      3. 数据验证：
         - 根据‘reduired’/'positive'促发不同的验证方法
         - 如果数据不存在，则提升错误
         - 如果数据，其中一个不存在，则提示错误
         - 如果数字小于0，则提示错误
```typescript
interface ValidateConfig{
    [propertyName: string]: { [validateProp: string]: string }
}
const pushValidate: ValidateConfig = {};

function RequiredTitle( target:any, name: string ){
    pushValidate[ target.constructor.name ] = { 
        ...pushValidate[ target.constructor.name ],
        [name]: 'required' 
    }
}

function RequiredNumber( target:any, name: string ){
    pushValidate[ target.constructor.name ] = { 
        ...pushValidate[ target.constructor.name ],
        [name]: 'positive'
    }
}
// 验证函数
function validate( objDate: object ){
    if( !objDate ){
        return true;
    }
    let result: boolean = true;
    console.log( pushValidate );
    for( let props in pushValidate ){
        for( let item in pushValidate[props] ){
            switch(pushValidate[props][item]){
                case 'required':
                    result = result && !!objDate[item]
                    break;
                case 'positive':
                    result = result && objDate[item] > 0
                    break; 
            }
        }
    }
    console.log( result );
    return result;
}

class Course {
    @RequiredTitle title: string;	// 装饰器监听
    @RequiredNumber price: number; // 装饰器监听
    constructor( title: string , price: number ){
        this.title = title;
        this.price = price;
    }
}

const currentFrom = document.getElementById("input-from")!;
currentFrom.addEventListener('submit',event=>{
    event?.preventDefault();
    const titleDoc = document.getElementById("title") as HTMLInputElement ;
    const priceDoc = document.getElementById("price") as HTMLInputElement ;
    const title: string = titleDoc.value.toString();
    const price: number = +priceDoc.value;

    const createCourse = new Course( title, price );
    console.log( createCourse );
    console.log( pushValidate );
    if( !validate( createCourse ) ){
        alert(" 输出错误!!! ");
    }
});
```
# 命名空间和模块
# 实战：拖动组件
### 流程

1. 拖动构建核心思路
1. 拖动目标配置:
   1. 开始拖动促发
      1. 监听"dragstart"
      1. 函数必备内容:
         1.  拖动传输数据: event.datatransfer!.setdata("text/plain",this.itemid ); // 可以传输文件链接等...
         1.  设定拖动类型: event.datatransfer!.dropeffect = "move";               // 拖动类型有copy等...
   2. 结束拖动促发:
      1. 监听"dragend"
      1. 函数必备内容:
         1. 根据情况可灵活配置
   3. html属性:
      1. 在拖动目标添加: draggable = "true"
      1. 例: `<div class="drag-target" draggable="true" ></div>`
3. 拖动放置区配置:
   1. 拖动目标时促发( "dragover"100ms促发一次 )
      1. 监听"dragover"
      1. 函数必备内容:
         1. 阻止默认( 核心 ): event.preventdefault(); // 必须要阻止默认，才会促发drop传输数据，拖动目标才被允许放置( 核心 )
         1. 放置区css变化: 可以改变放置区样式,更直观的显示可拖放位置// 拖动类型有copy等...
      3. 拖动目标,在放置区松开鼠标时促发
         1. 监听"drop"
         1. 函数必备内容:
            1. 获取传输数据: const dragdate = event.datatransfer!.getdata("text/plain");
            1. 根据传输的数据, 改变数据结构, 进行重新渲染目标
            1. 恢复放置区css变化
   2. 拖动目标, 离开放置区促发
      1. 监听"dragleave"
      1. 函数必备内容:
         1. 根据情况可灵活配置
### 源码
```typescript
//Drag & Drop Interfaces
interface Draggable {
    dragStartHandler(event: DragEvent): void;
    dragEndHandler(event: DragEvent): void;
}

interface DragTarget {
    dragOverHandler(event: DragEvent): void;
    dropHandler(event: DragEvent): void;
    dragLeaveHandler(event: DragEvent): void;
}

//Project Type
enum ProjectStatus {
    Active,
    Finished,
}

class Project {
    constructor(
        public id: string,
        public title: string,
        public description: string,
        public people: number,
        public status: ProjectStatus
    ) {}
}
type Listener<T> = (item: T[]) => void;

class State<T> {
    protected listeners: Listener<T>[] = [];

    addListener(listenerFn: Listener<T>) {
        this.listeners.push(listenerFn);
    }
}

// Project State  Management
class ProjectState extends State<Project> {
    private projects: Project[] = [];
    private static instance: ProjectState;

    private constructor() {
        super();
    }

    static getInstance() {
        if (this.instance) {
            return this.instance;
        }
        this.instance = new ProjectState();
        return this.instance;
    }

    addProject(title: string, description: string, numOfPeople: number) {
        const newProject = new Project(
            Math.random().toString(),
            title,
            description,
            numOfPeople,
            ProjectStatus.Active
        );
        this.projects.push(newProject);
        this.updateListeners();
    }
    removeProject(projectId: string, newStatus: ProjectStatus) {
        console.log(newStatus);
        const project = this.projects.find((p) => p.id === projectId);
        if (project && project.status !== newStatus) {
            project.status = newStatus;
            this.updateListeners();
        }
    }

    private updateListeners() {
        for (const listenerFn of this.listeners) {
            listenerFn(this.projects.slice());
        }
    }
}
const projectState = ProjectState.getInstance();

//Validation验证参数符合类型
interface Validation {
    value: string | number;
    required?: boolean;
    minLength?: number;
    maxLength?: number;
    min?: number;
    max?: number;
}

function validate(validatableInput: Validation) {
    let isValid = true;
    if (validatableInput.required) {
        isValid =
            isValid && validatableInput.value.toString().trim().length !== 0;
    }
    if (
        validatableInput.minLength != null &&
        typeof validatableInput.value === 'string'
    ) {
        isValid =
            isValid &&
            validatableInput.value.length >= validatableInput.minLength;
    }
    if (
        validatableInput.maxLength != null &&
        typeof validatableInput.value === 'string'
    ) {
        isValid =
            isValid &&
            validatableInput.value.length <= validatableInput.maxLength;
    }
    if (
        validatableInput.min != null &&
        typeof validatableInput.value === 'number'
    ) {
        isValid = isValid && validatableInput.value >= validatableInput.min;
    }

    if (
        validatableInput.max != null &&
        typeof validatableInput.value === 'number'
    ) {
        isValid = isValid && validatableInput.value <= validatableInput.max;
    }

    return isValid;
}

//autobing decoreator
function autobing(
    _target: any,
    _method: string,
    descriptor: PropertyDescriptor
) {
    const originalMethod = descriptor.value;
    const adjDescriptor: PropertyDescriptor = {
        configurable: true,
        get() {
            const boundFn = originalMethod.bind(this);
            return boundFn;
        },
    };
    return adjDescriptor;
}

//Component Base Class
abstract class Component<T extends HTMLElement, U extends HTMLElement> {
    templateElement: HTMLTemplateElement;
    hostElement: T;
    element: U;
    constructor(
        templateId: string,
        hostElementId: string,
        insertAtStart: boolean,
        newElementId?: string | undefined
    ) {
        this.templateElement = document.getElementById(
            templateId
        )! as HTMLTemplateElement;
        this.hostElement = document.getElementById(hostElementId)! as T;
        const importedNode = document.importNode(
            this.templateElement.content,
            true
        );
        this.element = importedNode.firstElementChild as U;
        if (newElementId) {
            this.element.id = newElementId;
        }

        this.attach(insertAtStart);
    }

    private attach(insertAtBeginning: boolean) {
        this.hostElement.insertAdjacentElement(
            insertAtBeginning ? 'afterbegin' : 'beforeend',
            this.element
        );
    }

    abstract configure?(): void;
    abstract renderContent(): void;
}

//ProjectItem Class
class ProjectItem
    extends Component<HTMLUListElement, HTMLLIElement>
    implements Draggable {
    private project: Project;

    get persons() {
        if (this.project.people === 1) {
            return '1 person';
        } else {
            return `${this.project.people} persions`;
        }
    }

    constructor(hostId: string, project: Project) {
        super('single-project', hostId, false, project.id);
        this.project = project;

        this.configure();
        this.renderContent();
    }

    @autobing
    dragStartHandler(event: DragEvent): void {
        event.dataTransfer!.setData('text/plain', this.project.id);
        event.dataTransfer!.effectAllowed = 'move';
    }

    @autobing
    dragEndHandler(event: DragEvent): void {
        console.log('dragEnd', event);
    }

    configure() {
        this.element.addEventListener('dragstart', this.dragStartHandler);
        this.element.addEventListener('dragend', this.dragEndHandler);
    }

    renderContent() {
        this.element.querySelector('h2')!.textContent = this.project.title;
        this.element.querySelector('h3')!.textContent =
            this.persons + ' assigned';
        this.element.querySelector('p')!.textContent = this.project.description;
    }
}

// ProjectList Class
class ProjectList
    extends Component<HTMLDivElement, HTMLElement>
    implements DragTarget {
    assignedProjects: Project[];

    constructor(private type: 'active' | 'finished') {
        super('project-list', 'app', false, `${type}-projects`);

        this.assignedProjects = [];

        this.configure();
        this.renderContent();
    }
    @autobing
    dragOverHandler(event: DragEvent): void {
        if (
            event.dataTransfer &&
            event.dataTransfer.types[0] === 'text/plain'
        ) {
            event.preventDefault();
            const listEl = this.element.querySelector('ul')!;
            listEl.classList.add('droppable');
        }
    }
    @autobing
    dropHandler(event: DragEvent): void {
        const prjId = event.dataTransfer!.getData('text/plain');
        projectState.removeProject(
            prjId,
            this.type === 'active'
                ? ProjectStatus.Active
                : ProjectStatus.Finished
        );
    }

    @autobing
    dragLeaveHandler(_: DragEvent): void {
        const listEl = this.element.querySelector('ul')!;
        listEl.classList.remove('droppable');
    }
    configure() {
        this.element.addEventListener('dragover', this.dragOverHandler);
        this.element.addEventListener('dragleave', this.dragLeaveHandler);
        this.element.addEventListener('drop', this.dropHandler);
        projectState.addListener((projects: Project[]) => {
            const relevantProjects = projects.filter((prj) => {
                if (this.type === 'active') {
                    return prj.status === ProjectStatus.Active;
                }
                return prj.status === ProjectStatus.Finished;
            });
            this.assignedProjects = relevantProjects;
            this.renderProjects();
        });
    }

    renderContent() {
        const listId = `${this.type}-projects-list`;
        this.element.querySelector('ul')!.id = listId;
        this.element.querySelector('h2')!.textContent =
            this.type.toUpperCase() + ' PROJECTS';
    }

    private renderProjects() {
        const listEl = document.getElementById(
            `${this.type}-projects-list`
        )! as HTMLUListElement;
        listEl.innerHTML = '';
        for (const prjItem of this.assignedProjects) {
            new ProjectItem(this.element.querySelector('ul')!.id, prjItem);
        }
    }
}

class ProjectInput extends Component<HTMLDivElement, HTMLFormElement> {
    titleInputElement: HTMLInputElement;
    descriptionInputElement: HTMLInputElement;
    peopleInputElement: HTMLInputElement;

    constructor() {
        super('project-input', 'app', true, 'user-input');

        this.titleInputElement = this.element.querySelector(
            '#title'
        ) as HTMLInputElement;
        this.descriptionInputElement = this.element.querySelector(
            '#description'
        ) as HTMLInputElement;
        this.peopleInputElement = this.element.querySelector(
            '#people'
        ) as HTMLInputElement;

        this.configure();
    }

    configure() {
        this.element.addEventListener('submit', this.submitHandler);
    }
    renderContent(): void {
        throw new Error('Method not implemented.');
    }

    private gatherUserInput(): [string, string, number] | void {
        const enteredTitle = this.titleInputElement.value;
        const enteredDescripttion = this.descriptionInputElement.value;
        const enteredPeople = this.peopleInputElement.value;

        const titleValidatable: Validation = {
            value: enteredTitle,
            required: true,
        };

        const descriptionValidatable: Validation = {
            value: enteredDescripttion,
            required: true,
            minLength: 5,
        };
        const peopleValidatable: Validation = {
            value: +enteredPeople,
            required: true,
            min: 1,
            max: 5,
        };

        if (
            !validate(titleValidatable) ||
            !validate(descriptionValidatable) ||
            !validate(peopleValidatable)
        ) {
            alert('Invalid input, please try again!');
            return;
        } else {
            return [enteredTitle, enteredDescripttion, +enteredPeople];
        }
    }

    private clearInput() {
        this.titleInputElement.value = '';
        this.descriptionInputElement.value = '';
        this.peopleInputElement.value = '';
    }

    @autobing
    private submitHandler(event: Event) {
        event.preventDefault();
        const userInput = this.gatherUserInput();
        if (Array.isArray(userInput)) {
            const [title, desc, people] = userInput;
            projectState.addProject(title, desc, people);
            this.clearInput();
        }
    }
}

const pje = new ProjectInput();
const active = new ProjectList('active');
const finished = new ProjectList('finished');

```
# Typescript+React
## create-react-app联用

- 使用 create-react-app xxxName - -typescript
## 防出错各种类型

- 类型：React.FC | **_ _**React.FromEvent
   - 表单传输类型: ( event: React.FromEvent )
   - React函数组件类型: React.FC
- REACT HOOKS | useRef使用
   - useRef<HTMLXxxxElement>(xx)获取内容:
      - xx.current：默认为ref目标的dom
      - xx.current:{ 子标签dom, 子标签dom, ... }
```typescript
import React, { useRef } from "react";

const RefTest: React.FC = () => {
  const textInputRef = useRef<HTMLInputElement>(null);
  const formTestRef = useRef<HTMLFormElement>(null);

  const submitHandler = (event: React.FormEvent) => {
    event.preventDefault();
    console.log(textInputRef.current!.value);

    console.log(formTestRef.current![0].name);	// 获取的为第一个子标签input
  };

  return (
    <form ref={formTestRef} onSubmit={submitHandler} className="test-useRef">
      <input ref={textInputRef} type="text" name="userName" id="userName" />
      <button type="submit">提交</button>
    </form>
  );
};

export default RefTest;

```

- 类型：HOOKS | useState设定类型
   - 防出错: useState<xxx>(xx);
- TYPESCRIPT的防出错类型，最好单独存放在"XXX.MODEL.TS"中提高代码可读性
```typescript
import React, { useState } from "react";

import { FormMsgType } from "./otherTest.model";	// xxx.model.ts

const OtherTest: React.FC = () => {
  const [formMsg, setFormMsg] = useState<FormMsgType>({ userName: "" });

  const changeHandler = (event: React.ChangeEvent) => {
    const { name, value } = event.target as any; // ts解构目标报错直接as any一把梭
    setFormMsg({ ...formMsg, [name]: value });
  };

  const submitHandler = (event: React.FormEvent) => {
    event.preventDefault();
  };

  return (
    <form onSubmit={submitHandler} className="test-useRef">
      <input
        onChange={changeHandler}
        value={formMsg.userName}
        type="text"
        name="userName"
        id="userName"
      />
      <button type="submit">提交</button>
    </form>
  );
};

export default OtherTest;
```
xxx.model.ts
```typescript
export interface FormMsgType {
  userName: string;
  addr?: string;
}
```

- 组件PROPS传输参数防出错写法
```typescript
import React from "react";

interface PropsFunctionTypesProps {
  contentString: (text: string) => void;
}

const PropsFunctionTypes: React.FC<PropsFunctionTypesProps> = props => {
  return <h1>{props.contentString}</h1>;	// 如果没有<PropsFunctionTypesProps>那么传输的props的数据在使用时将报错
};

export default PropsFunctionTypes;
```
# typescript在node.js | express配置
## 修改属性：
```typescript
"moduleResolution": "node",	// 告诉ts为node环境
"target": "es2018",  	// 编译为新版本，是为了在node下也能使用import/export代替require( 实验版 )
```

///
## 安装环境：
Express： yarn add express body-parser
Nodemon: yarn add nodemon - -dev    // 监听文件变动自动进行编译
## Types环境:
Node的ts环境：yarn add @types/node - -dev	// 有他则require将无报错
