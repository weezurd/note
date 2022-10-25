**Decode single input to single image**
- With HW accel
```
input_uri="rtsp://username:password@0.0.0.0:8554/live.stream"
gst-launch-1.0 rtspsrc location=$input_uri ! \
        rtph264depay ! \
        nvv4l2decoder enable-max-performance=1 ! \
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
**Decode multiple inputs to mutilple images (HW accel)**

We could probably use `nvmultistreamtiler` for a better view.
```
# Font
bold=$(tput bold)
normal=$(tput sgr0)

# Define cleanup function
pids=( )
cleanup() {
  for pid in "${pids[@]}"; do
    echo "Killing $pid"
    kill -0 "$pid" && kill "$pid" # kill process only if it's still running
  done
}

# Declare a string array with type
declare -a input_uris=(
    "rtsp://username:password@0.0.0.0:8554/live.stream1"
    "rtsp://username:password@0.0.0.0:8554/live.stream2"
    "rtsp://username:password@0.0.0.0:8554/live.stream3"
    "rtsp://username:password@0.0.0.0:8554/live.stream4"
)

# Read the array values with space
echo "${bold}Input uris:${normal}"
for val in "${input_uris[@]}"; do
  echo $val
done

echo 
echo "${bold}Decode uris in parallel to local images:${normal}"

trap cleanup EXIT TERM
counter=0
for input_uri in "${input_uris[@]}"; do
    # Decode to jpg image.
    counter=$(($counter+1))
    gst-launch-1.0 rtspsrc location=$input_uri ! \
            rtph264depay ! \
            nvv4l2decoder enable-max-performance=1 ! \
            nvvideoconvert interpolation-method=1 ! \
            videorate ! \
            "video/x-raw(memory:NVMM),format=RGBA,width=1920,height=1080,framerate=30/1" ! \
            nvvidconv ! \
            jpegenc ! \
            multifilesink location="$counter.jpg" & pids+=( "$!" )
done
wait
```
