<h1>Youtube Live Stream Guide</h1>
https://support.google.com/youtube/answer/2853702?hl=en


<h3>Stream Command</h3>

```
ffmpeg -f lavfi -i anullsrc -rtsp_transport udp -i "rtsp://username:password@ip_address:554/cam/realmonitor?channel=1&subtype=0" -force_key_frames "expr:gte(t,n_forced*2)" -vf scale=1920:1080 -reorder_queue_size 4000 -max_delay 10000000 -vcodec libx264 -b:v 4500k -pix_fmt yuv420p -f flv "<youtube_stream_url>"
```

<h3>Use Hardware Encoding on Mac</h3>

```
Change libx264 to h264_videotoolbox
```

<h3>Use Hardware Encoding on Windows (Intel processor, QuickSync)</h3>

```
Change libx264 to h264_qsv
```
