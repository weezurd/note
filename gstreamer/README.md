**Decode input to local image**
- With HW accel
```
input_uri="rtsp://username:password@0.0.0.0:8554/live.stream"
gst-launch-1.0 rtspsrc location=$input_uri ! \
        rtph264depay ! \
        nvv4l2decoder ! \
        nvvideoconvert interpolation-method=1 ! \
        videorate ! \
        "video/x-raw(memory:NVMM),format=RGBA,width=1920,height=1080,framerate=30/1" ! \
        nvvidconv ! \
        jpegenc ! \
        multifilesink location=frame.jpg
```
- Without HW accel
```
input_uri="rtsp://username:password@0.0.0.0:8554/live.stream"
gst-launch-1.0 rtspsrc location=$input_uri ! \
        rtph264depay ! \
        avdec_h264 ! \
        nvvideoconvert interpolation-method=1 ! \
        videorate ! \
        "video/x-raw" ! \
        jpegenc ! \
        multifilesink location=frame.jpg
```
