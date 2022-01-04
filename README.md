# Amazon Kinesis Video Streams Media Interface

[![main](https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface/actions/workflows/main.yml/badge.svg)](https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface/actions/workflows/main.yml)

## Introduction

**Amazon Kinesis Video Streams Media Interface** provides abstract interfaces for board specific media APIs. This repository also contains SoCs sensors/encoder implementations for Amazon Kinesis Video Streams Producer and WebRTC with out-of-box samples.

## Supported SoCs

- T31
- FH8626

## Getting started with out-of-box KVS WebRTC sample

1. Generate AWS credentials by using this [script](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c/blob/master/scripts/generate-iot-credential.sh)
2. Clone the code:
```bash
git clone https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface.git
```
3. Copy SoC SDK dependencies into *3rdparty/$BOARD/*. See [3rdparty/README.md](3rdparty/README.md).
4. Build the code:
```bash
cd amazon-kinesis-video-streams-media-interface
mkdir build; cd build; cmake .. -DBOARD=${YOUR_BOARD_NAME}
make
```
5. Upload built artifacts(i.e kvswebrtcmaster-static) at *amazon-kinesis-video-streams-media-interface/build/samples/webrtc/* to your board.
6. Setup AWS credentials in enjoinment variables. The sample is using AWS IoT certificates by default:
```bash
export AWS_KVS_LOG_LEVEL=2
export AWS_DEFAULT_REGION=us-east-1
export AWS_KVS_CACERT_PATH=cacert.pem
export AWS_IOT_CORE_THING_NAME=your_camera_name
export AWS_IOT_CORE_CREDENTIAL_ENDPOINT=xxxxxxxxxxxxxx.credentials.iot.us-east-1.amazonaws.com
export AWS_IOT_CORE_CERT=your_camera_certificate.pem
export AWS_IOT_CORE_PRIVATE_KEY=your_camera_private.key
export AWS_IOT_CORE_ROLE_ALIAS=your_camera_role_alias
```
Alternatively, you can choose use access key id/access key secret by turn off `IOT_CORE_ENABLE_CREDENTIALS` in [Samples.h](https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface/blob/main/samples/webrtc/source/Samples.h#L67) and setup credentials:
```bash
export AWS_KVS_LOG_LEVEL=2
export AWS_DEFAULT_REGION=us-east-1
export AWS_KVS_CACERT_PATH=cacert.pem
export AWS_ACCESS_KEY_ID=xxxxxxxxxxxxxxxxxxxx
export AWS_SECRET_ACCESS_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```
7. Execute sample on your board: `./kvswebrtcmaster-static`. If you are using access key id/access key secret, execute sample with channel name as parameter: `./kvswebrtcmaster-static your_channel_name`
8. Check WebRTC live stream via AWS console or [AWS WebRTC test page](https://awslabs.github.io/amazon-kinesis-video-streams-webrtc-sdk-js/examples/index.html)

## Getting started with out-of-box KVS Producer sample

1. Clone the code:
```bash
git clone https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface.git
```
2. Copy SoC SDK dependencies into *3rdparty/$BOARD/*. See [3rdparty/README.md](3rdparty/README.md).
3. Build the code:
```bash
cd amazon-kinesis-video-streams-media-interface
mkdir build; cd build; cmake .. -DBUILD_WEBRTC_SAMPLES=OFF -DBUILD_KVS_SAMPLES=ON -DBOARD=${YOUR_BOARD_NAME}
make
```
4. Upload built artifacts(i.e kvsproducer-static) at *amazon-kinesis-video-streams-media-interface/build/samples/kvs/* to your board.
5. Setup AWS credentials in enjoinment variables. The sample is using AWS access key/secret key by default:
```
export AWS_ACCESS_KEY_ID=xxxxxxxxxxxxxxxxxxxx
export AWS_SECRET_ACCESS_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
export AWS_DEFAULT_REGION=us-east-1
export AWS_KVS_HOST=kinesisvideo.us-east-1.amazonaws.com
```
6. Execute sample on your board: `./kvsproducer-static $YOUR_STREAM_NAME`
7. Check DASH live stream via [AWS KVS test page](https://aws-samples.github.io/amazon-kinesis-video-streams-media-viewer/)
8. Download video clips via AWS console.

> In current stage, browser doesn't support G.711 via HLS/DASH. To verify audio content in G.711 formats, user must download video clips.

## Getting started with out-of-box save frame sample

1. Clone the code:
```
git clone https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface.git
```
2. Copy SoC SDK dependencies into *3rdparty/$BOARD/*. See [3rdparty/README.md](3rdparty/README.md).
3. Build the code:
```bash
cd amazon-kinesis-video-streams-media-interface
mkdir build; cd build; cmake .. -DBUILD_WEBRTC_SAMPLES=OFF -DBUILD_SAVE_FRAME_SAMPLES=ON -DBOARD=${YOUR_BOARD_NAME}
make
```
4. Execute sample on your board: `./saveframe-static $FILE_NAME`

## Platform Implementation Guide

To adapt other platforms SDKs with **Amazon Kinesis Video Streams Media Interface**, you need to implement interfaces in *include/com/amazonaws/kinesis/video/capturer/VideoCapturer.h*, *include/com/amazonaws/kinesis/video/capturer/AudioCapturer.h* and *include/com/amazonaws/kinesis/video/player/AudioPlayer.h*:

- [VideoCapturer.h](include/com/amazonaws/kinesis/video/capturer/VideoCapturer.h): abstract interfaces defined for video capturer sensor/encoder.
- [AudioCapturer.h](include/com/amazonaws/kinesis/video/capturer/AudioCapturer.h): abstract interfaces defined for audio capturer sensor/encoder.
- [AudioPlayer.h](include/com/amazonaws/kinesis/video/player/AudioPlayer.h): abstract interfaces defined for audio playback sensor/encoder.

The implementations of those interfaces should be put into *source/${BOARD_NAME}* and follow the name rules:
- `${BOARD_NAME}VideoCapturer.c`
- `${BOARD_NAME}AudioCapturer.c`
- `${BOARD_NAME}AudioPlayer.c`

Then you need to add board specific source files and libs into [CMakeLists.txt](https://github.com/aws-samples/amazon-kinesis-video-streams-media-interface/blob/main/CMakeLists.txt#L23):

```cmake
if(BOARD STREQUAL "MY_BOARD")
    set(MY_BOARD_SDK_DIR ${CMAKE_CURRENT_LIST_DIR}/3rdparty/MY_BOARD/)
    # Add board related SDK sources here
    set(BOARD_SRCS
    )
    # Add board related SDK header here
    set(BOARD_INCS_DIR
        ${MY_BOARD_SDK_DIR}/include/
    )
    set(USE_MUCLIBC ON)
    # Add board related SDK lib here
    set(BOARD_LIBS_SHARED
        pthread
    )
    set(BOARD_LIBS_STATIC
        pthread
    )
endif()
```

## Contributing

[Contributing Guide](CONTRIBUTING.md)

## Code Of Conduct

[Code Of Conduct](CODE_OF_CONDUCT.md)

## License

[Apache 2.0](LICENSE)
