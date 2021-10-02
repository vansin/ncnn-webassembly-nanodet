# 浏览器端实现目标检测推理(基于wasm)

![](https://moonstarimg.oss-cn-hangzhou.aliyuncs.com/picgo_img/det.gif)



## 体验地址

https://det.vansin.top/

## 复现步骤

### 1. 安装emscripten工具链

此工具链用于把C++编译成WebAssembly 

```shell
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install 2.0.8
./emsdk activate 2.0.8

source emsdk/emsdk_env.sh
```

注意以下所有的操作都需要先 source emsdk/emsdk_env.sh

### 2. 编译测试ncnn

```shell
git clone https://github.com/Tencent/ncnn.git
mkdir build
cd build
```

如果是针对iOS中Safari等浏览器编译的，没有SIMD SSE2，使用以下代码编译

```shell
cmake -DCMAKE_TOOLCHAIN_FILE=../emsdk/upstream/emscripten/cmake/Modules/Platform/Emscripten.cmake -DNCNN_OPENMP=OFF -DNCNN_SIMPLEOMP=OFF -DNCNN_RUNTIME_CPU=OFF -DNCNN_SSE2=OFF -DNCNN_AVX2=OFF -DNCNN_BUILD_TESTS=ON ..
```

```shell
make -j
```

测试

```shell
TESTS_EXECUTABLE_LOADER=node ctest --output-on-failure -j 2
```

如果测试通过

```shell
make install
```

![](https://moonstarimg.oss-cn-hangzhou.aliyuncs.com/picgo_img/20211002154326.png)

### 3. 编译ncnn-webassembly-nanodet



```shell
git clone https://github.com/vansin/ncnn-webassembly-nanodet
cd ncnn-webassembly-nanodet
```

把编译好的ncnn的install复制到ncnn-webassembly-nanodet目录下

![](https://moonstarimg.oss-cn-hangzhou.aliyuncs.com/picgo_img/20211002155458.png)

```shell
mkdir build
cd build

cmake -DCMAKE_TOOLCHAIN_FILE=$EMSDK/upstream/emscripten/cmake/Modules/Platform/Emscripten.cmake -DWASM_FEATURE=basic ..

make -j
```



![](https://moonstarimg.oss-cn-hangzhou.aliyuncs.com/picgo_img/20211002155815.png)



把index.html和wasmFeatureDetect.js复制到build中

```shell
cp ../index.html index.html
cp ../wasmFeatureDetect.js wasmFeatureDetect.js
```



全局安装http-server，并启动

```shell
npm install -g http-server
http-server .
```

![](https://moonstarimg.oss-cn-hangzhou.aliyuncs.com/picgo_img/20211002160130.png)

![](https://moonstarimg.oss-cn-hangzhou.aliyuncs.com/picgo_img/20211002160159.png)

## 参考

https://github.com/nihui/ncnn-webassembly-nanodet

https://waittim.github.io/2020/11/15/build-ncnn-wasm/