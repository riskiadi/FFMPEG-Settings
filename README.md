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
nullsrc=size=854x480, drawbox=x=0:w=854:h=480:t=fill:c=black[base]; \
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
nullsrc=size=1280x720[base]; \
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


