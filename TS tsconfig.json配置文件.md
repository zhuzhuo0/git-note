## TS tsconfig.json配置文件

```
 {
  "compileOnSave": false,
  "compilerOptions": {
    // 允许编译javascript文件。
    "allowJs": true,
    // 允许从没有设置默认导出的模块中默认导入。这并不影响代码的显示，仅为了类型检查。
    "allowSyntheticDefaultImports": true,
    // 不报告执行不到的代码错误
    "allowUnreachableCode": true,
    // 不报告未使用的标签错误。
    "allowUnusedLabels": true,
    // 以严格模式解析并为每个源文件生成 "use strict"语句
    "alwaysStrict": false,
    // 解析非相对模块名的基准目录。查看 模块解析文档了解详情。
    "baseUrl": "./",
    // 输入文件的字符集。
    "charset": "utf8",
    // 在 .js文件中报告错误。与 --allowJs配合使用。
    "checkJs": true,
    // 生成相应的 .d.ts文件。
    "declaration": true,
    // 生成声明文件的输出路径。
    "declarationDir": "./@types/",
    // 显示诊断信息。
    "diagnostics": true,
    // 禁用JavaScript工程体积大小的限制
    "disableSizeLimit": false,
    // 禁止对同一个文件的不一致的引用。
    "forceConsistentCasingInFileNames": false,
    // 从 tslib 导入辅助工具函数（比如 __extends， __rest等）
    // "importHelpers": true,
    "outDir": "./dist/out-tsc",
    // 生成单个sourcemaps文件，而不是将每sourcemaps生成不同的文件。
    "inlineSourceMap": false,
    // 将代码与sourcemaps生成到一个文件中，要求同时设置了 --inlineSourceMap或 --sourceMap属性。
    "inlineSources": false,
    // 将每个文件作为单独的模块（与“ts.transpileModule”类似）。
    "isolatedModules": false,
    // 在 .tsx文件里支持JSX： "React"或 "Preserve"。查看 JSX。
    "jsx": "preserve",
    // 指定生成目标为react JSX时，使用的JSX工厂函数，比如 React.createElement或 h。
    "jsxFactory": "React.createElement",
    "sourceMap": true,
    "moduleResolution": "node",
    // 给源码里的装饰器声明加上设计类型元数据。查看 issue #2577了解更多信息。
    "emitDecoratorMetadata": true,
    // 启用实验性的ES装饰器。
    "experimentalDecorators": true,
    // 打印出编译后生成文件的名字
    "listEmittedFiles": true,
    // 编译过程中打印文件名。
    "listFiles": true,
    "target": "es5",
    // node_modules依赖的最大搜索深度并加载JavaScript文件。仅适用于 --allowJs。
    "maxNodeModuleJsDepth": 1,
    "module": "umd",
    "typeRoots": ["node_modules/@types"],
    // 删除所有注释，除了以 /!*开头的版权信息。
    "removeComments": true,
    "lib": ["es2017", "dom"],
    "noImplicitAny": true,
    "paths": {

    }
  }
}
```

#### 常用

```
{
  "compileOnSave": false,
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true,
    "allowSyntheticDefaultImports": true,
    "removeComments": true,
    "maxNodeModuleJsDepth": 0,
    "allowUnreachableCode": true,
    "allowUnusedLabels": true,
    "alwaysStrict": false,
    "baseUrl": "./",
    "charset": "utf8",
    "declaration": true,
    "disableSizeLimit": false,
    "outDir": "./dist/out-tsc",
    "sourceMap": true,
    "moduleResolution": "node",
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "target": "es5",
    "lib": ["es2017", "dom"],
    "noImplicitAny": true
    "paths": {

    }
  }
}
```

#### Compiler Options

| Option                               | Type       | Default                                                      | Description                                                  |
| ------------------------------------ | ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `--allowJs`                          | `boolean`  | `false`                                                      | 允许JavaScript文件被编译。                                   |
| `--allowSyntheticDefaultImports`     | `boolean`  | `module === "system"` or `--esModuleInterop` is set and `module` is not `es2015`/`esnext` | 允许从没有默认导出的模块进行默认导入。这不会影响代码运行，只是为了类型检测。 |
| `--allowUnreachableCode`             | `boolean`  | `false`                                                      | 不要报告无法访问的代码上的错误。                             |
| `--allowUnusedLabels`                | `boolean`  | `false`                                                      | 不要在未使用的标签上报告错误。                               |
| `--alwaysStrict`                     | `boolean`  | `false`                                                      | 在严格模式下解析并在每个文件内添加"use strict"。             |
| `--baseUrl`                          | `string`   |                                                              | 用于解析非相对路径模块名称的根目录，有关详细信息，请参阅 [Module Resolution documentation](./Module Resolution.md#base-url) 。 |
| `--charset`                          | `string`   | `"utf8"`                                                     | 输入文件的字符集。                                           |
| `--checkJs`                          | `boolean`  | `false`                                                      | 报告.js文件中的错误。与allowJs一起使用。                     |
| `--declaration` `-d`                 | `boolean`  | `false`                                                      | 自动生成相应的.d.ts文件。                                    |
| `--declarationDir`                   | `string`   |                                                              | 生成的声明文件的输出目录。                                   |
| `--declarationMap`                   | `boolean`  | `false`                                                      | 为每个对应的“.d.ts”文件生成map文件。                         |
| `--diagnostics`                      | `boolean`  | `false`                                                      | 显示诊断信息。                                               |
| `--disableSizeLimit`                 | `boolean`  | `false`                                                      | 禁用JavaScript项目的大小限制。                               |
| `--downlevelIteration`               | `boolean`  | `false`                                                      | 提供迭代器全面支持。                                         |
| `--emitBOM`                          | `boolean`  | `false`                                                      | 在输出文件的头部添加UTF-8字节顺序标记（BOM）。               |
| `--emitDeclarationOnly`              | `boolean`  | `false`                                                      | 仅添加'.d.ts'声明文件。                                      |
| `--emitDecoratorMetadata`[1]         | `boolean`  | `false`                                                      | 在源文件内添加设计类型元数据。See [issue #2577](https://github.com/Microsoft/TypeScript/issues/2577) for details. |
| `--esModuleInterop`                  | `boolean`  | `false`                                                      | Emit `__importStar` and `__importDefault` helpers for runtime babel ecosystem compatibility and enable `--allowSyntheticDefaultImports` for typesystem compatibility. |
| `--experimentalDecorators`[1]        | `boolean`  | `false`                                                      | 为ES6的装饰器启用实验支持。                                  |
| `--extendedDiagnostics`              | `boolean`  | `false`                                                      | 显示详细的诊断信息。                                         |
| `--forceConsistentCasingInFileNames` | `boolean`  | `false`                                                      | 禁止对同一文件的不一致引用。                                 |
| `--help` `-h`                        |            |                                                              | 打印帮助信息。                                               |
| `--importHelpers`                    | `boolean`  | `false`                                                      | 导入添加助手 (e.g. `__extends`, `__rest`, etc..) from [`tslib`](https://www.npmjs.com/package/tslib) |
| `--inlineSourceMap`                  | `boolean`  | `false`                                                      | 使用souce map生成单个文件，而不是使用单独的文件。            |
| `--inlineSources`                    | `boolean`  | `false`                                                      | 在单独的文件内生成source。需要设置  `--inlineSourceMap` 或者 `--sourceMap`。 |
| `--init`                             |            |                                                              | 初始化TypeScript项目并创建tsconfig.json文件。                |
| `--isolatedModules`                  | `boolean`  | `false`                                                      | 将每个文件透明化为单独的模块（类似于“ts.transpileModule”）。 |
| `--jsx`                              | `string`   | `"Preserve"`                                                 | 在.tsx文件中支持JSX ："React"或"Preserve"。See [JSX](./JSX.md). |
| `--jsxFactory`                       | `string`   | `"React.createElement"`                                      | 指定JSX工厂函数，以便在定位反应JSX发射时使用，例如React.createElement或h。 |
| `--keyofStringsOnly`                 | `boolean`  | `false`                                                      | 仅解析keyof为字符串值属性名称（无数字或符号）。              |
| `--lib`                              | `string[]` |                                                              | 要包含在编译中的库文件列表。 可能的值有：  ► `ES5`  ► `ES6`  ► `ES2015`  ► `ES7`  ► `ES2016`  ► `ES2017`   ► `ES2018`  ► `ESNext`  ► `DOM`  ► `DOM.Iterable`  ► `WebWorker`  ► `ScriptHost`  ► `ES2015.Core`  ► `ES2015.Collection`  ► `ES2015.Generator`  ► `ES2015.Iterable`  ► `ES2015.Promise`  ► `ES2015.Proxy`  ► `ES2015.Reflect`  ► `ES2015.Symbol`  ► `ES2015.Symbol.WellKnown`  ► `ES2016.Array.Include`  ► `ES2017.object`  ► `ES2017.Intl`  ► `ES2017.SharedMemory`  ► `ES2017.TypedArrays`  ► `ES2018.Intl`  ► `ES2018.Promise`  ► `ES2018.RegExp`  ► `ESNext.AsyncIterable`  ► `ESNext.Array`  ► `ESNext.Intl`  ► `ESNext.Symbol`   注意：如果--lib未指定库的默认列表注入。注入的默认库包括：   ► For `--target ES5`: `DOM,ES5,ScriptHost`   ► For `--target ES6`: `DOM,ES6,DOM.Iterable,ScriptHost` |
| `--listEmittedFiles`                 | `boolean`  | `false`                                                      | 打印编译的部分生成文件的名称。                               |
| `--listFiles`                        | `boolean`  | `false`                                                      | 打印编译部分文件的名称。                                     |
| `--locale`                           | `string`   | *(platform specific)*                                        | 用于显示错误消息的语言环境，例如en-us。  可能的值包括：  ► English (US): `en`  ► Czech: `cs`  ► German: `de`  ► Spanish: `es`  ► French: `fr`  ► Italian: `it`  ► Japanese: `ja`  ► Korean: `ko`  ► Polish: `pl`  ► Portuguese(Brazil): `pt-BR`  ► Russian: `ru`  ► Turkish: `tr`  ► Simplified Chinese: `zh-CN`   ► Traditional Chinese: `zh-TW` |
| `--mapRoot`                          | `string`   |                                                              | 指定调试器应该找到映射文件而不是生成的位置的位置。如果.map文件将在运行时位于与.js文件不同的位置，请使用此标志。指定的位置将嵌入到sourceMap中，以指示调试器将映射文件所在的位置。 |
| `--maxNodeModuleJsDepth`             | `number`   | `0`                                                          | 在node_modules下搜索并加载JavaScript文件的最大依赖关系深度。仅适用于--allowJs。 |
| `--module` `-m`                      | `string`   | `target === "ES3" or "ES5" ? "CommonJS" : "ES6"`             | 指定模块代码生成："None"，"CommonJS"，"AMD"，"System"，"UMD"，"ES6"，"ES2015"或"ESNext"。 ► 仅"AMD"和"System"可以一起使用--outFile。 ► 当目标编译`"ES5"`或者更低时可能使用`"ES6"` 和 `"ES2015"` |
| `--moduleResolution`                 | `string`   | `module === "AMD" or "System" or "ES6" ? "Classic" : "Node"` | 确定如何解决模块。无论是"Node"使用Node.js语言/ io.js风格的分辨率，或"Classic"。有关详细信息，请参阅[Module Resolution documentation](./Module Resolution.md) |
| `--newLine`                          | `string`   | *(platform specific)*                                        | 使用发出文件时指定的行结束序列:( "crlf"windows）或"lf"（unix）。“ |
| `--noEmit`                           | `boolean`  | `false`                                                      | 不生成文件                                                   |
| `--noEmitHelpers`                    | `boolean`  | `false`                                                      | 不在已生成的文件里生成类似`__extends`的自定义工具库          |
| `--noEmitOnError`                    | `boolean`  | `false`                                                      | 如果报告任何错误，不生成文件。                               |
| `--noErrorTruncation`                | `boolean`  | `false`                                                      | 不截断错误消息。                                             |
| `--noFallthroughCasesInSwitch`       | `boolean`  | `false`                                                      | 报告switch语句中的fallthrough案例的错误。                    |
| `--noImplicitAny`                    | `boolean`  | `false`                                                      | 使用隐含any类型提高表达式和声明的错误。                      |
| `--noImplicitReturns`                | `boolean`  | `false`                                                      | 当函数中的所有代码路径都没有返回值时报告错误。               |
| `--noImplicitThis`                   | `boolean`  | `false`                                                      | this使用隐含any类型时提示错误。                              |
| `--noImplicitUseStrict`              | `boolean`  | `false`                                                      | 不在输出模块上生成`"use strict"`。                           |
| `--noLib`                            | `boolean`  | `false`                                                      | 不要包含默认库文件(`lib.d.ts`)。                             |
| `--noResolve`                        | `boolean`  | `false`                                                      | 不要将三斜杠引用或模块导入目标添加到已编译文件列表中。       |
| `--noStrictGenericChecks`            | `boolean`  | `false`                                                      | 禁止严格检查函数类型中的通用签名。                           |
| `--noUnusedLocals`                   | `boolean`  | `false`                                                      | 报告未使用的局部变量的错误。                                 |
| `--noUnusedParameters`               | `boolean`  | `false`                                                      | 报告未使用参数的错误。                                       |
| ~~`--out`~~                          | `string`   |                                                              | 被移除DEPRECATED. 请使用--outFile代替。                      |
| `--outDir`                           | `string`   |                                                              | 将输出结构重定向到目录。                                     |
| `--outFile`                          | `string`   |                                                              | 连接并将输出提交到单个文件。连接的顺序由在命令行上传递给编译器的文件列表以及三斜杠引用和导入确定。有关详细信息，请参阅输出文件顺序文档。 |
| `paths`[2]                           | `Object`   |                                                              | 模块名称到相对于的位置的路径映射条目列表baseUrl。有关详细信息，请参阅[Module Resolution documentation](./Module Resolution.md#path-mapping) 。 |
| `--preserveConstEnums`               | `boolean`  | `false`                                                      | 不要在生成的代码中删除const枚举声明。有关更多详细信息，请参阅 [const enums documentation]。(https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md#94-constant-enum-declarations) for more details. |
| `--preserveSymlinks`                 | `boolean`  | `false`                                                      | 不要将符号链接解析为真正的路径; 将符号链接的文件视为真实文件。 |
| `--preserveWatchOutput`              | `boolean`  | `false`                                                      | 在监视模式下保持过时的控制台输出，而不是清除屏幕。           |
| `--pretty`                           | `boolean`  | 使用颜色和上下文来设置错误和消息的样式。                     |                                                              |
| `--project` `-p`                     | `string`   |                                                              | 在给定有效配置文件的情况下编译项目。 参数可以是有效JSON配置文件的文件路径，也可以是包含文件的目录的目录路径tsconfig.json。 有关更多详细信息，请参阅 [tsconfig.json](./tsconfig.json.md) 。 |
| `--reactNamespace`                   | `string`   | `"React"`                                                    | DEPRECATED. 使用--jsxFactory代替。                           |
| `--removeComments`                   | `boolean`  | `false`                                                      | 删除所有注释，除了以右侧开头的副本标题注释 /*!               |
| `--resolveJsonModule`                | `boolean`  | `false`                                                      | 包含使用.json扩展名导入的模块。                              |
| `--rootDir`                          | `string`   |                                                              | 指定输入文件的根目录。仅用于控制输出目录结构--outDir。       |
| `rootDirs`[2]                        | `string[]` |                                                              | 根文件夹列表，其组合内容表示运行时项目的结构。See [Module Resolution documentation](./Module Resolution.md#virtual-directories-with-rootdirs) for more details. |
| `--skipDefaultLibCheck`              | `boolean`  | `false`                                                      | DEPRECATED.使用--skipLibCheck代替。 Skip type checking of [default library declaration files](./Triple-Slash Directives.md#-reference-no-default-libtrue). |
| `--skipLibCheck`                     | `boolean`  | `false`                                                      | 跳过所有声明文件的类型检查（*.d.ts）。                       |
| `--sourceMap`                        | `boolean`  | `false`                                                      | 生成相应的.map文件。                                         |
| `--sourceRoot`                       | `string`   |                                                              | 指定调试器应该找到TypeScript文件而不是源位置的位置。如果源位于运行时位于与设计时不同的位置，请使用此标志。指定的位置将嵌入到sourceMap中，以指示源文件所在的调试器。 |
| `--strict`                           | `boolean`  | `false`                                                      | 启用所有严格类型检查选项。 `--strict` 启用 `--noImplicitAny`, `--noImplicitThis`, `--alwaysStrict`, `--strictNullChecks`, `--strictFunctionTypes` 和 `--strictPropertyInitialization`。 |
| `--strictFunctionTypes`              | `boolean`  | `false`                                                      | 禁用功能类型的双变量参数检查。                               |
| `--strictPropertyInitialization`     | `boolean`  | `false`                                                      | 确保在构造函数中初始化非未定义的类属性。需要--strictNullChecks启用此选项才能生效。 |
| `--strictNullChecks`                 | `boolean`  | `false`                                                      | 在严格的空检查模式中，null和undefined值不在每种类型的域中，并且只能分配给它们any（undefined可以分配的一个例外void）。 |
| `--stripInternal`[1]                 | `boolean`  | `false`                                                      | 不要为具有/** @internal */JSDoc注释的代码发出声明。          |
| `--suppressExcessPropertyErrors`     | `boolean`  | `false`                                                      | 禁止对象文字的多余属性检查。                                 |
| `--suppressImplicitAnyIndexErrors`   | `boolean`  | `false`                                                      | 抑制`--noImplicitAny`索引缺少索引签名的对象的错误。有关详细信息，请参阅[问题＃1232](https://github.com/Microsoft/TypeScript/issues/1232#issuecomment-64510362)。 |
| `--target` `-t`                      | `string`   | `"ES3"`                                                      | 指定ECMAScript的目标版本："ES3"（默认）， "ES5"，"ES6"/ "ES2015"，"ES2016"，"ES2017"或"ESNext"。   注意：`"ESNext"`目标是最新支持的[ES提议功能](https://github.com/tc39/proposals)。 |
| `--traceResolution`                  | `boolean`  | `false`                                                      | 报告模块解析日志消息。                                       |
| `--types`                            | `string[]` |                                                              | 要包括的类型定义的名称列表。有关详细信息[，](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#types-typeroots-and-types)请参阅[@types，-typeRoots和-types](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#types-typeroots-and-types)。 |
| `--typeRoots`                        | `string[]` |                                                              | 要包含类型定义的文件夹列表。有关详细信息[，](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#types-typeroots-and-types)请参阅[@types，-typeRoots和-types](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#types-typeroots-and-types)。 |
| `--version` `-v`                     |            |                                                              | 打印编译器的版本。                                           |
| `--watch` `-w`                       |            |                                                              | 在监视模式下运行编译器。观察输入文件并在更改时触发重新编译。可以使用环境变量配置观察文件和目录的实现。有关详细信息，请参阅[配置监视](https://www.typescriptlang.org/docs/handbook/configuring-watch.html) |

-  [1] 这些选项是实验性的。
-  [2] 这些选项仅允许使用tsconfig.json，而不能通过命令行开关。

## Related

- 在[`tsconfig.json`](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)文件中设置编译器选项。
- 在[MSBuild项目中](https://www.typescriptlang.org/docs/handbook/compiler-options-in-msbuild.html)设置编译器选项。