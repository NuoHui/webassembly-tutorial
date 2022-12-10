# 在JS中调用C函数

在真正实际业务中，接入一个完备功能的Webassemble模块，必然是需要提供给JS层调用的函数接口的。本示例中，我们通过简单demo,展示如何在C函数中导出api供js层使用。

## 定义函数导出宏

为了方便函数导出，我们需要先定义一个函数导出宏，该宏需要完成以下功能：

- 避免函数因为缺乏引用而导致在编译时被优化器删除。如果某个导出函数仅供JavaScript调用，而在C/C++环境中从未被使用，开启某些优化选项（比如-O2以上）时，函数有可能被编译器优化删除，因此需要提前告知编译器：该函数必须保留，不能删除，不能改名。
因此，你可以使用 Emscripten 中的 ccall() 函数，以及 EMSCRIPTEN_KEEPALIVE 声明（将你的函数添加到导出函数列表中（详见 Why do functions in my C/C++ source code vanish when I compile to JavaScript, and/or I get No functions to process?））


你可以看到我们这导出了一个`myFunction`函数。

```c
#include <stdio.h>
#include <emscripten/emscripten.h>

int main(int argc, char ** argv) {
    printf("Hello World\n");
}

#ifdef __cplusplus
extern "C" {
#endif

int EMSCRIPTEN_KEEPALIVE myFunction(int argc, char ** argv) {
    printf("我的函数已被调用\n");
}

#ifdef __cplusplus
}
#endif
```

编译命令

```shell
emcc -o hello.html Hello.c -O3 -s WASM=1 -s "EXTRA_EXPORTED_RUNTIME_METHODS=['ccall']"
```

在html中编写js调用逻辑。


```html
<button class="mybutton">运行我的函数</button>
<script>
    document.querySelector('.mybutton').addEventListener('click', function(){
        alert('检查控制台');
        var result = Module.ccall('myFunction', // name of C function
            null, // return type
            null, // argument types
            null); // arguments
    });
</script>
```

![截屏2022-12-10 21.02.41.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/45438af8c1f5422987d5e2d3ec2225e4~tplv-k3u1fbpfcp-watermark.image?)

当然你这里也可以不用ccall()的方式，你会发现`myFunction`直接导出可以被调用。


![1.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/539507af4b95458c812748075328c7e9~tplv-k3u1fbpfcp-watermark.image?)


</script>你
</script>

