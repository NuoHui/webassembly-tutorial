# Emscripten Compiler Frontend

本文主要介绍Emscripten 编译器前端 (emcc) 从命令行调用 Emscripten 编译器的一些常规使用。

## 命令行语法

```shell
emcc [options] file...
```

由于`emcc`实际上是 `gcc` 或 `clang` 等标准编译器的直接替代品。因此大多数 `clang` 选项，`gcc` 选项在`emcc`都是可以使用的。

下面介绍一些常用的参数优化。

- -O0

[编译+链接] 无优化（默认）。这是开始移植项目的推荐设置，因为它包含各种断言。 此优化设置和其他优化设置在编译期间和链接期间都有意义。在编译期间它会影响 LLVM 优化，在链接期间它会影响 Binaryen 中代码的最终优化以及 JS 的优化。 （对于快速增量构建 -O0 是最好的，而对于发布产物我们应该选择其他高级优化。）


- -O1

[编译+链接] 简单的优化。在编译步骤中，这些包括 LLVM -O1 优化。在链接步骤中，这不包括 -O0 会执行的 JS 中的各种运行时断言。

- -O2

[compile+link] 与 -O1 类似，但启用了更多优化。在链接期间，这还将启用各种 JavaScript 优化。因此对于我们的 wasm应该开启这配置。
简单理解这些 JavaScript 优化可以通过删除编译器中未使用的代码来减少代码大小，特别是，如果这些代码未在 Module 对象上导出，则可能会删除部分运行时。


- -O3

[compile+link] 与 -O2 类似，但具有额外的优化，可能需要更长的运行时间。在生产环境还是推荐使用的。


- -Os
[compile+link] 与 -O3 类似，但更关注代码大小（并且可能会与速度进行权衡）。这会影响 wasm 和 JavaScript。


- -Oz

[compile+link] 与 -Os 类似，但进一步减少了代码大小，并且可能需要更长的运行时间。这会影响 wasm 和 JavaScript。

后续针对代码优化，我们可以阅读[optimizing-code](https://emscripten.org/docs/optimizing/Optimizing-Code.html#optimizing-code).


- -g

[编译+链接] 保留调试信息。 当编译成目标文件时，这与在 Clang 和 gcc 中一样，它会在目标文件中添加 DWARF 调试信息。 链接时，这相当于 -g3。

因此这个选项开启是可以用于我们进行调试的。

- -g<level>
  
[compile+link] 控制可调试性级别。每个级别都建立在前一个级别的基础上：

1. -g0 保持基本代码可调试.
2. -g1 链接时，保留 JavaScript 中的空格。
3. -g2 链接时，保留编译代码中的函数名称。
4. -g3 编译为目标文件时，保留调试信息，包括 JS 空格、函数名称和 LLVM 调试信息 (DWARF)（如果有）（这与 -g 相同）。

  
- --profiling
  
带上调试信息，便于 Chrome DevTools 调试。
[如果在编译时传递，则与 -g2 相同，否则在链接处应用] 在发出 JavaScript 时使用合理的默认值以使构建可读但仍然对分析有用。这会设置 -g2（保留空格和函数名称）并且还可以启用影响性能的优化，否则可能不会在 -g2 中执行。
  
- --profiling-funcs
[链接] 在分析中保留函数名称，但在其他方面减少空格和名称，就像我们通常在优化构建中所做的那样。如果您想查看基于函数名称的探查器结果，但不打算读取发出的代码，这将很有用。
  
- --closure 0|1|2
[链接] 运行 Closure Compiler。可能的值是：
  
0: No closure compiler (default in -O2 and below).
1: 运行closure compiler，这大大减少了支持 JavaScript 代码的大小（除了 WebAssembly 或 asm.js 之外的所有代码）。请注意，这会显着增加编译时间。
2: 对所有的代码运行closure compiler，即使是在 asm.js 模式下的 asm.js 输出上。这可以进一步减少代码大小，但确实会阻止大量 asm.js 优化，因此不建议这样做，除非您想不惜一切代价减少代码大小。  
  
注意：
如果闭包编译器遇到内存不足，请尝试在环境中调整 JAVA_HEAP_SIZE（例如，4GB 为 4096m）。
只有在执行 JavaScript 选项（-O2 或以上）时才会运行Closure Compiler。
  

- --bind
[链接] 针对嵌入库的链接。弃用：改用 -lembind。对于我们需要将C、C++方法导出给js调用需要开启。

  
-sOPTION[=VALUE]
  
不同的选项影响不同的阶段，大多数在链接时] Emscripten 构建选项。有关可用选项，请参阅 [settings](https://github.com/emscripten-core/emscripten/blob/main/src/settings.js)。
如果没有指定值，它将默认为 1。

比如:
-s MALLOC=dlmalloc：一个强大的通用 malloc.
-s ALLOW_MEMORY_GROWTH=1: 允许内存增长。
-s INITIAL_MEMORY: 设置 wasm 模块中的初始内存大小，如果没有开启ALLOW_MEMORY_GROWTH，那么也就是最大内存大小。
-s PRECISE_F32
-s DISABLE_EXCEPTION_CATCHING emcc异常处理相关配置
-s USE_PTHREADS：是否开启pthreads
  
  
 


  





