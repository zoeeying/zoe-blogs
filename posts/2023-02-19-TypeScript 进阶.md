# TypeScript 进阶

## 1. 配置文件

在项目中，通过执行命令 `tsc --init` 可以生成  tsconfig.json 文件，它是 TypeScript 编译器的配置文件，指定了用来编译这个项目的根文件和编译选项。

在项目中，执行 `tsc demo.ts` 命令编译单个 TS 文件时，tsconfig.json 会被忽略，而是使用默认的编译配置，只有执行 `tsc` 命令时，tsconfig.json 才会生效。

下面介绍 tsconfig.json 中具体配置项的含义。

| 配置项          | 默认值                                                  | 描述                                                 |
| --------------- | ------------------------------------------------------- | ---------------------------------------------------- |
| include         | `["**/*"]`                                              | 指定哪些 TS 文件需要被编译。                         |
| exclude         | `["node_modules", "bower_components", "jspm_packages"]` | 指定哪些 TS 文件不需要被编译。                       |
| extends         | /                                                       | 指定被继承的配置文件。                               |
| files           | /                                                       | 指定被编译的具体的文件。                             |
| compilerOptions | /                                                       | 编译选项，包含多个**子选项**，用来完成对编译的配置。 |

compilerOptions 子选项如下。

| 配置项           | 默认值  | 描述                                                         |
| ---------------- | ------- | ------------------------------------------------------------ |
| target           | `"ES3"` | 指定 TS 被编译成的 ES 版本。`"ESNext"` 表示最新版本的 ES。   |
| module           | /       | 指定要使用的模块化规范。可以设置 `"commonjs"`、`"amd"`、`"es6"` 等。推荐使用 `"es6"`。 |
| lib              | `[]`    | 指定项目中要使用的库。可以设置 `"dom"`、`"es6"`、`"es2015.promise"`、`"webworker"` 等值。一般不需要手动配置。 |
| incremental      | false   | 增量编译，如果配置为 true，那么只会编译新增的内容。          |
| rootDir          | `./`    | 指定编译源码的根目录。                                       |
| outDir           | `./`    | 指定编译后 JS 文件所在的目录。                               |
| outFile          | `./`    | 将 TS 文件中**全局作用域**的代码编译后合并到同一个文件中。不建议使用。 |
| allowJs          | false   | 配置是否对 JS 文件进行编译。                                 |
| checkJs          | false   | 配置是否检查 JS 代码是否符合 TS 语法规范。                   |
| removeComments   | false   | 配置是否移除注释。                                           |
| noEmit           | false   | 设置成 true，表示不生成编译后的文件，一般用于不想使用 TS 的编译功能，而只想使用 TS 对语法进行检查。 |
| noEmitOnError    | false   | 设置成 true，表示有语法错误时，编译会报错，且不会生成编译后的文件。 |
| alwaysStrict     | false   | 设置编译后的 JS 文件是否使用严格模式。如果使用了导出、引入模块，JS 代码会自动使用严格模式。 |
| noImplicitAny    | false   | 设置成 true，表示不允许隐式的 any 类型，如果类型是 any，也需要手动写出来。 |
| noImplicitThis   | false   | 设置成 true，表示不允许不明确类型的 this。                   |
| strictNullChecks | false   | 设置成 true，表示对于有可能的空值，必须严格检查处理，并且 null 值也不能赋给具体类型的变量。 |
| strict           | false   | 所有严格检查的总开关。                                       |
| types            | /       | 指定只使用哪些全局类型声明，而不是 node_modules/@types 下所有的类型声明。比如，`"types": ["node"]` 表示 node_modules/@types/node。 |
| typeRoots        | /       | 指定只寻找特定目录下的类型声明文件。                         |
| baseUrl          | /       | 设置`baseUrl`来告诉编译器到哪里去查找模块，即 TypeScript 项目的根路径。所有非相对模块导入都会被当做相对于 `baseUrl`。 |
| paths            | /       | 用来做路径重映射。需要先设置 baseUrl。比如，`"@/*": ["./src/*"]` 表示把相对于 baseUrl 的 src 路径重映射成 @。 |

## 2. 声明文件

单独使用的模块，一般会同时提供一个单独的**类型声明文件**（declaration file），把本模块的**外部接口**的所有类型都写在这个文件里面，便于模块使用者了解接口，也便于编译器检查使用者的用法是否正确。

类型声明文件：用来为已存在的 JS 库提供类型信息。

类型声明文件以 .d.ts 结尾，只能包含**类型信息**，不能包含**可执行代码**。

.ts 文件是 implementation，即代码实现文件；.d.ts 文件是 declaration，即类型声明文件。

内置类型声明文件：TS 为 JS 运行时可用的所有标准化内置 API 都提供了声明文件。

## 3. declare 关键字

declare 关键字可以描述以下类型。

- 变量（const、let、var 命令声明）  declar const a:string
- type 或者 interface 命令声明的类型
- class
- enum
- 函数（function）
- 模块（module）
- 命名空间（namespace）

declare 只能用来描述已经存在的变量和数据结构，不能用来声明新的变量和数据结构。另外，所有 declare 语句都不会出现在编译后的文件里面。

declare 关键字可以进行模块的类型扩展：

```javascript
// 为模块moduleA中的Foo接口扩充一个custom属性
declare module 'moduleA' {
  interface Foo {
    custom: {
      prop1: string;
    }
  }
}
```

declare global 用法：

```javascript
export {};

declare global {
  interface String {
    toSmallString(): string;
  }
}

// 具体实现
String.prototype.toSmallString = ():string => {
  return '';
};
```

`export {}` 作用是强制编译器将这个脚本当作**模块**处理，这是因为 declare global 必须用在模块里面。

看到一个解释，如果是 .d.ts 声明文件中，任何的 declare 默认就是 global 的，因此在 .d.ts 文件中不能出现 declare global，只有在模块文件中，如果想要定义全局的可以使用 declare global。
