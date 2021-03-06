<h1>Youtube Live Stream Guide</h1>
https://support.google.com/youtube/answer/2853702?hl=en


<h3>Stream Command</h3>

```
ffmpeg -f lavfi -i anullsrc -rtsp_transport tcp -i "rtsp://username:password@ip_address:554/cam/realmonitor?channel=1&subtype=0" -force_key_frames "expr:gte(t,n_forced*2)" -vf scale=1920:1080 -reorder_queue_size 4000 -max_delay 10000000 -vcodec libx264 -b:v 4500k -pix_fmt yuv420p -f flv "<youtube_stream_url>"
```

<h3>Use Hardware Encoding on Mac</h3>

```
Change libx264 to h264_videotoolbox
```

<h3>Use Hardware Encoding on Windows (Intel processor, QuickSync)</h3>

```
Change libx264 to h264_qsv
```

<h3>FFMPEG Error Log</h3>

```
crccheck : verify embedded CRCs

bitstream: detect bitstream specification deviations

buffer: detect improper bitstream length

explode: abort decoding on minor error detection

ignore_err: ignore decoding errors, and continue decoding. This is useful if you want to analyze the content of a video and thus want everything to be decoded no matter what. This option will not result in a video that is pleasing to watch in case of errors.

careful: consider things that violate the spec and have not been seen in the wild as errors

compliant: consider all spec non compliancies as errors

aggressive: consider things that a sane encoder should not do as an error
```

<h3>Setting Positions</h3>


* Top left: x=0:y=0 (with 10 pixel padding x=10:y=10)
* Top center: x=(w-text_w)/2:y=0 (with 10 px padding x=(w-text_w)/2:y=10)
* Top right: x=w-tw:y=0 (with 10 px padding: x=w-tw-10:y=10)
* Centered: x=(w-text_w)/2:y=(h-text_h)/2
* Bottom left: x=0:y=h-th (with 10 px padding: x=10:y=h-th-10)
* Bottom center: x=(w-text_w)/2:y=h-th (with 10 px padding: x=(w-text_w)/2:y=h-th-10)
* Bottom right: x=w-tw:y=h-th (with 10 px padding: x=w-tw-10:y=h-th-10)


<h1>My FFMPEG Presets</h1>

<h3>Screenshot Preset</h3>

```
ffmpeg -rtsp_transport tcp -y -i "rtsp://192.168.100.60/live/ch00_1" -vframes 1 cctv.jpg
```

<h3>Local Preset (3 Source)</h3>

```
ffmpeg -rtsp_transport tcp \
-i "rtsp://192.168.100.50/live/ch00_1" \
-i "rtsp://192.168.100.60/live/ch00_1" \
-i "rtsp://192.168.100.70/live/ch00_1" \
-filter_complex " \
[0:v] setpts=PTS-STARTPTS, scale=800x448,setsar=1[first]; \
[1:v] setpts=PTS-STARTPTS, scale=800x448,setsar=1[second]; \
[2:v] setpts=PTS-STARTPTS, scale=800x448,setsar=1[third]; \
[first][second][third]hstack=inputs=3[v]" \
-map "[v]" test.mp4
```

<h3>Local Preset (4 Source)</h3>

```
ffmpeg -rtsp_transport tcp \
-i "rtsp://192.168.100.50/live/ch00_1" \
-i "rtsp://192.168.100.60/live/ch00_1" \
-i "rtsp://192.168.100.70/live/ch00_1" \
-i "rtsp://192.168.100.80/live/ch00_1" \
-filter_complex " \
[0:v] setpts=PTS-STARTPTS, scale=854:480,setsar=1[upperleft]; \
[1:v] setpts=PTS-STARTPTS, scale=854:480,setsar=1[upperright]; \
[2:v] setpts=PTS-STARTPTS, scale=854:480,setsar=1[bottomleft]; \
[3:v] setpts=PTS-STARTPTS, scale=854:480,setsar=1[bottomright]; \
[upperleft][upperright]hstack[topbase]; \
[bottomleft][bottomright]hstack[bottombase]; \
[topbase][bottombase]vstack[base]" \
-map [base] -map 4 test.mp4
```

<h3>Youtube Streaming Preset (1 Source)</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.60/live/ch00_1" \
-f lavfi -i anullsrc \
-preset veryfast \
-vcodec libx264 \
-force_key_frames "expr:gte(t,n_forced*2)" \
-threads 6 \
-qscale 3 \
-b:v 4000k \
-pix_fmt yuv420p \
-f flv \
rtmp://a.rtmp.youtube.com/live2/XXX-XXX-XXX
```

<h3>Youtube Streaming Preset (3 Source)</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.50/live/ch00_1" \
-i "rtsp://192.168.100.60/live/ch00_1" \
-i "rtsp://192.168.100.70/live/ch00_1" \
-f lavfi -i anullsrc \
-filter_complex " \
[0:v] setpts=PTS-STARTPTS, scale=1280x720,setsar=1[first]; \
[1:v] setpts=PTS-STARTPTS, scale=1280x720,setsar=1[second]; \
[2:v] setpts=PTS-STARTPTS, scale=1280x720,setsar=1[third]; \
[first][second][third]hstack=inputs=3[v]" \
-map [v] -map 3 \
-preset veryfast -vcodec libx264 -b:v 3000k -pix_fmt yuv420p \
-f flv rtmp://a.rtmp.youtube.com/live2/XXX.XXX.XXX \
```
<h3>Youtube Streaming Preset (2 Source) v.2</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.60/live/ch00_1" \
-rtsp_transport tcp \
-i "rtsp://192.168.100.70/live/ch00_1" \
-f lavfi -i anullsrc \
-filter_complex " \
[0:v] setpts=PTS-STARTPTS, scale=427:480,setsar=1:1[first]; \
[1:v] setpts=PTS-STARTPTS, scale=427:480,setsar=1:1[second]; \
[first][second]hstack=inputs=2[v]" \
-map [v] -map 2 \
-preset superfast -vcodec libx264 -force_key_frames "expr:gte(t,n_forced*2)" -threads 6 -qscale 1 -b:v 4000k -pix_fmt yuv420p \
-f flv rtmp://a.rtmp.youtube.com/live2/XXXX-XXXX-XXXX-XXXX-XXXX
```

<h3>Youtube Streaming Preset (3 Source) v.2</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.50/live/ch00_1" \
-i "rtsp://192.168.100.60/live/ch00_1" \
-i "rtsp://192.168.100.70/live/ch00_1" \
-f lavfi -i anullsrc \
-filter_complex " \
[0:v] setpts=PTS-STARTPTS, scale=iw:ih,setsar=1:1[first]; \
[1:v] setpts=PTS-STARTPTS, scale=iw:ih,setsar=1:1[second]; \
[2:v] setpts=PTS-STARTPTS, scale=iw:ih,setsar=1:1[third]; \
[first][second][third]hstack=inputs=3[v]" \
-map [v] -map 3 \
-preset veryfast -vcodec libx264 -force_key_frames "expr:gte(t,n_forced*2)" -threads 6 -qscale 3 -b:v 4000k -pix_fmt yuv420p \
-f flv rtmp://a.rtmp.youtube.com/live2/XXX.XXX.XXX \
```

<h3>Youtube Streaming Preset (2 Source) v.3</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.70/live/ch00_1" \
-rtsp_transport tcp \
-i "rtsp://192.168.100.60/live/ch00_1" \
-f lavfi -i anullsrc \
-filter_complex " \
nullsrc=size=854x480, drawbox=x=0:w=854:h=480:t=fill:c=black, drawtext=text='%{localtime}':fontsize=20:fontcolor=white:x=(w-text_w)/2:y=15[base]; \
[0:v] setpts=PTS-STARTPTS, scale=427x240[upperleft]; \
[1:v] setpts=PTS-STARTPTS, scale=427x240[upperright]; \
[base][upperleft] overlay=shortest=1:x=0:y=125[tmp1]; \
[tmp1][upperright] overlay=shortest=1:x=427:y=125" \
-preset veryfast -vcodec libx264 -threads 6 -b:v 4000k -pix_fmt yuv420p \
-f flv rtmp://a.rtmp.youtube.com/live2/XXX.XXX.XXX \
```

<h3>Youtube Streaming Preset (4 Source) v.3</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.70/live/ch00_1" \
-rtsp_transport tcp \
-i "rtsp://192.168.100.60/live/ch00_1" \
-rtsp_transport tcp \
-i "rtsp://192.168.100.50/live/ch00_1" \
-f lavfi -i anullsrc \
-filter_complex " \
nullsrc=size=1280x720, drawbox=x=0:w=1280:h=720:t=fill:c=black, drawtext=text='%{localtime}':fontsize=20:fontcolor=white:x=(w-text_w)/2:y=15[base]; \
[0:v] setpts=PTS-STARTPTS, scale=640x360[upperleft]; \
[1:v] setpts=PTS-STARTPTS, scale=640x360[upperright]; \
[2:v] setpts=PTS-STARTPTS, scale=640x360[lowerleft]; \
[base][upperleft] overlay=shortest=1:x=0:y=0[tmp1]; \
[tmp1][upperright] overlay=shortest=1:x=640:y=0[tmp2]; \
[tmp2][lowerleft] overlay=shortest=1:x=0:y=360" \
-preset veryfast -vcodec libx264 -threads 6 -qscale 3 -b:v 4000k -pix_fmt yuv420p \
-f flv rtmp://a.rtmp.youtube.com/live2/XXX.XXX.XXX \
```

<h3>Youtube EMERGENCY Streaming Preset Single Source</h3>

```
ffmpeg \
-rtsp_transport tcp \
-i "rtsp://192.168.100.70/live/ch00_1" \
-f lavfi -i anullsrc \
-preset veryfast -vcodec libx264 -force_key_frames "expr:gte(t,n_forced*2)" -threads 6 -qscale 3 -b:v 4000k -pix_fmt yuv420p \
-f flv rtmp://a.rtmp.youtube.com/live2/XXXX.XXXX.XXXX.XXXX.XXXX
```


