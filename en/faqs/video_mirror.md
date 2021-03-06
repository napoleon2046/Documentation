
---
title: Mirror images occur in a call.
description: 视频镜像
platform: All Platforms
updatedAt: Mon Jul 01 2019 15:12:10 GMT+0800 (CST)
---
# Mirror images occur in a call.
Mirror images may occur in the following scenarios:

## Step 1: Self-check

* If the user sees mirror images on the remote side, check if the app calls the `setParameters(audioEngine.remoteVideoMirroring)` method.
* If the user sees mirror images on the local side:
	* Images taken by the front camera are commonly mirrored.
	* If the images are not taken by the front camera, check if the app calls the `setParameters(che.video.localViewMirrorSetting)` method.

## Step 2: Contact Agora Customer Support

If the issue persists, contact Agora customer support and submit the issue with the following information:
* If the front or rear camera is used.
* If the mirror image is on the local or remote side.
* If you prefer the mirror or true image.
