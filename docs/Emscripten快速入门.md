# Emscripten快速入门

当你在用 C/C++ 之类的语言编写模块时，你可以使用Emscripten来将它编译到 WebAssembly。让我们来看看它是如何工作的。

## 准备环境安装

下载源码自己编译一个Emscripten。

```shell
git clone https://github.com/juj/emsdk.git
cd emsdk

# 在 Linux 或者 Mac macOS 上
./emsdk install --build=Release sdk-incoming-64bit binaryen-master-64bit
./emsdk activate --global --build=Release sdk-incoming-64bit binaryen-master-64bit
# 如果在你的 macos 上获得以下错误
Error: No tool or SDK found by name 'sdk-incoming-64bit'
# 请执行
./emsdk install latest
# 按照提示配置环境变量即可
./emsdk activate latest


# 在 Windows 上
emsdk install --build=Release sdk-incoming-64bit binaryen-master-64bit
emsdk activate --global --build=Release sdk-incoming-64bit binaryen-master-64bit

# 注意：Windows 版本的 Visual Studio 2017 已经被支持，但需要在 emsdk install 需要追加 --vs2017 参数。
```

具体安装过程我们可以参考[编译 C/C++ 为 WebAssembly](https://developer.mozilla.org/zh-CN/docs/WebAssembly/C_to_wasm)。
