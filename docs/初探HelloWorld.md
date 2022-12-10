# 初探 Hello World

本节将从经典的"Hello, wolrd!"例程入手，介绍如何使用Emscripten编译C/C++代码并运行测试。

准备一段C代码, Hello.c。

```c
#include <stdio.h>

int main(int argc, char ** argv) {
  printf("Hello World\n");
}
```

现在，转到一个已经配置过 Emscripten 编译环境的终端窗口中，进入刚刚保存 Hello.c 文件的文件夹中，然后运行下列命令：

```shell
emcc Hello.c -s WASM=1 -o hello.html
```

我们简单看一段参数配置：

- -s WASM=1 — 指定我们想要的 wasm 输出形式。如果我们不指定这个选项，Emscripten 默认将只会生成asm.js。
- -o hello.html — 指定这个选项将会生成 HTML 页面来运行我们的代码，并且会生成 wasm 模块，以及编译和实例化 wasm 模块所需要的“胶水”js 代码，这样我们就可以直接在 web 环境中使用了。

我们现在可以看到在您的源码文件夹应该有下列文件:

- hello.wasm 二进制的 wasm 模块代码
- hello.js 一个包含了用来在原生 C 函数和 JavaScript/wasm 之间转换的胶水代码的 JavaScript 文件
- hello.html 一个用来加载，编译，实例化你的 wasm 代码并且将它输出在浏览器显示上的一个 HTML 文件

现在我们可以通过在当前目录下执行

```shell
http-server -g
```

现在使用一个支持 WebAssembly 的浏览器，加载生成的 hello.html 。

如果一切顺利，你应该可以在页面上的 Emscripten 控制台和 浏览器控制台 中看到 "Hello World" 的输出。

恭喜！你已经成功将 C 代码编译成 JavaScript 并且在浏览器中执行了！

![HelloWorld](https://raw.githubusercontent.com/NuoHui/typora-imgs/main/202212101033955.png)
