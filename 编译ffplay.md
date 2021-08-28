
#### 编译sdl2
安装sdl2才能编译ffplay

```
wget https://www.libsdl.org/release/SDL2-2.0.16.tar.gz
tar xzf SDL2-2.0.16.tar.gz 
cd SDL2-2.0.16
mkdir build
cd build
cmake ..
make -j 10
make install
```

如果安装了libiconv，可能会报错
> sdl undefined reference to `libiconv_open'

因为libc中也有iconv相关的，编译的时候找到了libiconv的头文件却链接的libc，就有些符号找不到。
可以看看/usr/include和/usr/local/include是否都有iconv.h且不一样。
可以把/usr/local/include/iconv.h备份一下，然后把/usr/include/iconv.h拷贝过去。

#### 编译ffplay
```
./configure --enable-ffplay --enable-sdl2 --disable-x86asm    // 检查ffbuild/config.mak中CONFIG_FFPLAY=yes
make -j 10
make install 
```

#### 运行ffplay
如果在没有音频和视频输出的设备上，需要分别使用参数-an和-nodisp来避免SDL初始化音频和视频，否则会出现错误：

> Could not initialize SDL  

-an会把整个音频流去掉（不会解码）。     
-nodisp在ffplay设置了display_disable，display_disable又会设置video_disable，这会导致视频流也被干掉（不会解码）     
音频流和视频流都没打开就会报错：
> Failed to open file 'http://vod-test-1258344699.cos.ap-guangzhou.myqcloud.com/quanyou1.f30.mp4' or configure filtergraph

至少应保留视频流，所以应修改代码，让display_disable时不去设置video_disable。

运行

```
./ffplay -an -nodisp -loglevel info http://vod-test-1258344699.cos.ap-guangzhou.myqcloud.com/quanyou1.f30.mp4
```