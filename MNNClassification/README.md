
# 编译MNN和模型转换
1. 安装cmake 3.12
```bash
# 卸载旧的cmake
sudo apt-get autoremove cmake

# 下载cmake3.12
wget https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz
tar zxvf cmake-3.12.2-Linux-x86_64.tar.gz

# 移动目录并添加软连接
sudo mv cmake-3.12.2-Linux-x86_64 /opt/cmake-3.12.2
sudo ln -sf /opt/cmake-3.12.2/bin/*  /usr/bin/
```

2. 添加Android NDK

```bash
wget https://dl.google.com/android/repository/android-ndk-r21b-linux-x86_64.zip
unzip android-ndk-r21b-linux-x86_64.zip
# 添加环境变量，留意你实际下载地址
export ANDROID_NDK=/mnt/d/android-ndk-r21b
```

3. 开始编译

```bash
https://github.com/alibaba/MNN.git
cd MNN/

# 编译armv7动态库和armv8动态库
./schema/generate.sh
cd project/android/
mkdir build_32 && cd build_32 && ../build_32.sh
mkdir build_64 && cd build_64 && ../build_64.sh
```

复制`build_32`生成的so全部复制到`armeabi-v7a`下，`armeabi-v7a`需要新建。复制`build_64`生成的so全部复制到`arm64-v8a`下，`arm64-v8a`需要新建，这两个文件备用，下面创建Android项目需要使用到。

4. 转换模型，首先要编译转换模型的工具`MNNConvert`，命令如下，记得是在MNN项目的根目录执行。
```bash
sudo apt-get install autoconf automake libtool curl make g++ unzip
git clone https://github.com/google/protobuf.git
cd protobuf
git submodule update --init --recursive
./autogen.sh
./configure
make -j4
make check
sudo make install
sudo ldconfig

cd MNN/
mkdir build
cd build
cmake .. -DMNN_BUILD_CONVERTER=true && make -j4
```

模型转换，MNN提供多种模型转换方式，以下介绍几种主流模型的转换方式，Tensorflow模型有两个中
```bash
./MNNConvert -f TF --modelFile XXX.pb --MNNModel XXX.mnn --bizCode 0001
```

```bash
./MNNConvert -f TFLITE --modelFile XXX.tflite --MNNModel XXX.mnn --bizCode 0001
```

```bash
./MNNConvert -f ONNX --modelFile XXX.onnx --MNNModel XXX.mnn --bizCode 0001
```

# 开发Android项目

2. 把MNN跟目录下的`include`复制到Android项目的`app`目录下，

3. 把上一步编译得到的`armeabi-v7a`和`arm64-v8a`目录复制到`main/jniLibs`下。

4. 把`MNN/demo/android/app/src/main/jni`中的C++代码复制到`main/cpp`下。

5. 把`MNN/demo/android/app/src/main/java/com/taobao/android/mnn`整个文件复制到Android的复制Java的包目录下。