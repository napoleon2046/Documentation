
---
title: Push Streams to CDN
description: 
platform: iOS,macOS
updatedAt: Tue Dec 10 2019 04:20:41 GMT+0800 (CST)
---
# Push Streams to CDN
## Introduction

The process of publishing streams into the CDN (Content Delivery Network) is called CDN live streaming, where users can view the live broadcast through a web browser.

When multiple hosts are in a channel in CDN live streaming, [transcoding](https://docs.agora.io/en/AgoraPlatform/terms?platform=AllPlatforms#transcoding) is used to combine the streams of all the hosts into a single stream. Transcoding sets the audio/video profiles and the picture-in-picture layout for the stream to be pushed to the CDN.

![](https://web-cdn.agora.io/docs-files/1569414336352)

## Prerequisites

Ensure that you enable the RTMP Converter service before using this function.

1. Log in [Console](https://dashboard.agora.io/), and click ![img](https://web-cdn.agora.io/docs-files/1551260936285) in the left navigation menu to go to the **Products & Usage** page. 
2. Select a project from the drop-down list in the upper-left corner, and click **Duration** under **RTMP Converter**. 
![](https://web-cdn.agora.io/docs-files/1569302661254)
3. Click **Enable RTMP Converter**.
4. Click **Apply** to enable the RTMP Converter service and get 500 max concurrent channels.

<div class="alert note"> The number of concurrent channels, N, means that users can push streams to the CDN with transcoding in N channels of media streams. </div>

Now, you can use the function and see the usage statistics.


## Implementation 

Before proceeding, ensure that you implement a basic live broadcast in your project. See [Start a Live Broadcast](../../en/Interactive%20Broadcast/start_live_ios.md) for details.

Refer to the following steps to push streams to the CDN:

<a name="single"></a>
1. The host in a channel calls the `setLiveTranscoding` method to set the transcoding parameters of media streams (`AgoraLiveTranscoding`), such as the resolution, bitrate, frame rate and position of watermark/background. If you need a transcoded picture, set the picture-in-picture layout for each user in the `transcodingUser` class, as described in [Sample Code](#sample).

   > The `rtcEngineTranscodingUpdated` callback occurs when the `AgoraLiveTranscoding` class updates, and reports update information to the local host.

2. The host in a channel calls the `addPublishStreamUrl` method to add a media stream to the CDN. 

   > Use `transcodingEnabled` to set whether transcoding is enabled or not.

3. The host in a channel calls the `removePublishStreamUrl` method to remove a media stream from the CDN live broadcast.


When the state of media streams pushed to the CDN changes, SDK triggers the `rtmpStreamingStateChangedtoState` callback to report current state of pushing streams to the local host. The local host can troubleshoot with the error code when exceptions occur.



<a name="sample"></a>
### Sample code

```objective-c
// Objective-C
// Sets the output layout for each user.
AgoraLiveTranscodingUser *user = [[AgoraLiveTranscodingUser alloc] init];
// The uid must be identical to the uid used in joinChannel().
user.uid = 12345;
user.rect = CGRectMake(0, 0, 640, 720);
user.audioChannel = 0;

AgoraRtcEngineKit *rtcEngine = [AgoraRtcEngineKit sharedEngineWithAppId:@"" delegate:nil];
// CDN transcoding settings.
AgoraLiveTranscoding *transcoding = [[AgoraLiveTranscoding alloc] init];
transcoding.audioSampleRate = AgoraAudioSampleRateType44100;
transcoding.audioChannels = 2;
transcoding.audioBitrate = 48;
// Size of the video. The default value is 360 x 640 (px).
transcoding.size = CGSizeMake(360, 640);
// Bitrate of the video (Kbps). The default value is 400.
transcoding.videoBitrate = 400;
// Frame rate of the video (fps). The default value is 15. Agora adjusts all values over 30 to 30.
transcoding.videoFramerate = 30;
// Video codec profile. Choose to set as Baseline (66), Main (77), or High (100). If you set this parameter to other values, Agora adjusts it to the default value of 100.
transcoding.videoCodecProfile = AgoraVideoCodecProfileTypeHigh;
transcoding.transcodingUsers = @[user];

// CDN transcoding settings when using transcoding.
[rtcEngine setLiveTranscoding:transcoding];

// Adds a URL to which the host pushes a stream. Set the transcodingEnabled parameter as true to enable the transcoding service. Once transcoding is enabled, you need to set the live transcoding configurations by calling the setLiveTranscoding method. We do not recommend transcoding in the case of a single host.
[rtcEngine addPublishStreamUrl:streamUrl transcodingEnabled:YES];

// Removes a URL to which the host pushes a stream.
[rtcEngine removePublishStreamUrl:streamUrl];
```

**Example 1: Two-host Tile Horizontally**


<img alt="../_images/sei_2host.png" src="https://web-cdn.agora.io/docs-files/en/sei_2host.png" style="width: 416.0px; height: 240.0px;"/>


```
Canvas:
     width: 640
     height: 360
     backgroundColor: #FFFFFF

User0:
      userId: 123
      x: 0
      y: 0
      width: 320
      height: 360
      zOrder: 1
      alpha: 1.0

User1:
      userId: 456
      x: 320
      y: 0
      width: 320
      height: 360
      zOrder: 1
      alpha: 1.0
```

**Example 2: Three-host Tile Vertically**

<img alt="../_images/sei_3host.png" src="https://web-cdn.agora.io/docs-files/en/sei_3host.png" style="width: 236.0px; height: 416.0px;"/>

```
Canvas:
      width: 360
      height: 640
      backgroundColor: #FFFFFF

   User0:
      userId: 123
      x: 0
      y: 0
      width: 360
      height: 640
      zOrder: 1
      alpha: 1.0

   User1:
       userId: 456
       x: 0
       y: 320
       width: 180
       height: 320
       zOrder: 2
       alpha: 1.0

   User2:
       userId: 789
       x: 180
       y: 320
       width: 180
       height: 320
       zOrder: 2
       alpha: 1.0
```

**Example 3: One Full Screen + Floating Thumbnails**

<img alt="../_images/sei_random.png" src="https://web-cdn.agora.io/docs-files/en/sei_random.png" style="width: 828.0px; height: 416.0px;"/>


```
Canvas:
   width: 360
   height: 640
   backgroundColor: #FFFFFF

User0:
   userId: 123
   x: 0
   y: 0
   width: 360
   height: 640
   zOrder: 1
   alpha: 1.0

User1:
    userId: 456
    x: 45
    y: 390
    width: 110
    height: 213
    zOrder: 2
    alpha: 1.0

User2:
    userId: 789
    x: 185
    y: 390
    width: 110
    height: 213
    zOrder: 2
    alpha: 1.0
```

We also provide an open-source [Live-Streaming](https://github.com/AgoraIO/Advanced-Interactive-Broadcasting/tree/master/Live-Streaming) demo project on GitHub.

### API reference 

- [`setLiveTranscoding`](https://docs.agora.io/en/Interactive%20Broadcast/API%20Reference/oc/Classes/AgoraRtcEngineKit.html#//api/name/setLiveTranscoding:)
- [`addPublishStreamUrl`](https://docs.agora.io/en/Interactive%20Broadcast/API%20Reference/oc/Classes/AgoraRtcEngineKit.html#//api/name/addPublishStreamUrl:transcodingEnabled:)
- [`removePublishStreamUrl`](https://docs.agora.io/en/Interactive%20Broadcast/API%20Reference/oc/Classes/AgoraRtcEngineKit.html#//api/name/removePublishStreamUrl:)
- [`rtcEngineTranscodingUpdated`](https://docs.agora.io/en/Interactive%20Broadcast/API%20Reference/oc/Protocols/AgoraRtcEngineDelegate.html#//api/name/rtcEngineTranscodingUpdated:)
- [`rtmpStreamingStateChangedtoState`](https://docs.agora.io/en/Interactive%20Broadcast/API%20Reference/oc/Protocols/AgoraRtcEngineDelegate.html#//api/name/rtcEngine:rtmpStreamingChangedToState:state:errorCode:)

## Considerations

- Up to 17 hosts can be supported in the same live broadcast channel.

- In the case of a single host, we do not recommend transcoding. You can skip [Step1](#single), and call the `addPublishStreamUrl` method directly with `transcodingEnabled (false)`.

  > Currently, Agora only supports pushing H.264 (default) streams to the CDN.

- Set the value of `videoBitrate` by referring to [Video Bitrate Table](https://docs.agora.io/en/Interactive%20Broadcast/API%20Reference/cpp/structagora_1_1rtc_1_1_video_encoder_configuration.html#af10ca07d888e2f33b34feb431300da69). If you set a bitrate beyond the proper range, the SDK automatically adjusts it to a value within the range. 

- Agora charges a transcoding usage fee for CDN live streaming.
