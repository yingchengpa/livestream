# livestream

h264/h265 rtmp、http-flv live stream

ffmpeg srs4.x nginx-http-flv 支持hevc flv


# 搭建方法

- 使用ffmpeg读取文件，包括rtsp、mp4、flv等文件，并将视频内容变为rtmp流

~~~
ffmpeg -re -i movie.mp4 -vcodec copy -acodec copy -f flv rtmp://127.0.0.1:1935/live/1
or
ffmpeg  -i rtsp://xxx   -vcodec copy -an -f flv rtmp://127.0.0.1:1935/live/1
~~~

- 使用srs4.0 作为实时视频服务器，支持RTMP/WebRTC/HLS/HTTP-FLV/SRT

~~~
启动srs ./objs/srs -c conf/http.flv.live.conf
~~~

- 或者nginx-http-flv-module 作为服务器也可。 实际测试中，嵌入式环境压力测试下，会存在内存堆积导致重启，而srs正常。

- 使用vlc 播放， http://192.168.50.240/live/1.flv 

# hevc(h265) 支持

由于flv不支持hevc（h265），所以需要参考code=12的模式，在ffmpeg和srs中修改flv对hevc的兼容。
修改方法如仓库代码。

# 播放器

- 可以采用修改后支持flv h265的ffplay

- vlc（不支持播放http-flv和rtmp的h265）

- 可以使用easyplayer.js 、 wxinlineplayer.js 等js播放器，也可选择一些商用js播放器，性能/稳定性更高。

# js播放器介绍
解码原理：
1）使用mse解码模式：支持h264。 采用c++ cpu解码，不依赖gpu。优点：性能好、平台兼容性好  缺点：不支持h265。
2）wasm解码模式：支持h264、h265。采用js cpu解码。优点：平台兼容性好，支持h265. 缺点：性能一般。
3）WebCodecs硬解码：支持h264、h265。采用 gpu解码。 优点：性能好，支持h265。 缺点：平台兼容性一般。
我个人推荐的做法是使用mse模式解码，但Chromium内核由于专利的原因只支持h264的解码方式，
如果要支持hevc的mse解码模式，需要重新编译Chromium内核，并放开对hevc的支持。在实测中，360浏览器是支持hevc解码的。
或者使用仓库中的Chromium浏览器也默认支持hevc。

所以在选择js播放器时，需要能够同时支持以上解码方式，并在延时、流畅、兼容性上做到平衡。
