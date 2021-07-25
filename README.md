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

- 或者nginx-http-flv-module 作为服务器也可

- 使用vlc 播放， http://192.168.50.240/live/1.flv 

# hevc(h265) 支持

由于flv不支持hevc（h265），所以需要参考code=12的模式，在ffmpeg和srs中修改flv对hevc的兼容

# 播放器

- 可以采用修改后支持flv h265的ffplay

- 可以使用easyplayer.js 、 wxinlineplayer.js 等播放器

# js播放器性能

使用wasm模式解码的js播放器，在高码率的h264或hevc视频时，性能不足，导致播放不流畅。推荐的做法是使用mse模式解码，Chromium内核默认只支持h264的解码方式，
如果要支持hevc的mse解码模式，需要重新编译Chromium内核，并放开对hevc的支持。在实测中，360极速浏览器默认是支持hevc解码的，其他播放器都不支持。