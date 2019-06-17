---
layout: post
title: Overview about media - codec, container, streaming
tags:
- Media
---

## RTSP vs RTP

Real Time Streaming Protocol (RTSP)

- Application Layer protocol, similar to HTTP, SSH...
- Controlling protocol only, not data transporting
- Used to stream any data in any format (text, audio, video...)
- Client-server

[Some request types](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol):

- `OPTIONS`: similar to HTTP OPTIONS, server will response list of allowed request types
- `DESCRIBE`: description of data will be received
- `SETUP`, `PLAY`, `PAUSE`...

RTSP only provides establishing and controlling, not transmission.

For transmission, other protocols are used, like Real-time Transport Protocol (RTP)

Real-time Transport Protocol (RTP)

- Delivering audio and video over IP networks
- Combined with other controlling/signaling protocol
- Support profile, each profile with many payload formats
  - Profile defines codecs of payload
  - Payload type is defined in RTP package header

## Media codec vs container

[Media codecs](https://en.wikipedia.org/wiki/Codec)

- Algos/methods for encoding and decoding data
- Specific for compression, quality...
- Popular codecs:
  - [Video](https://en.wikipedia.org/wiki/Comparison_of_video_codecs)
  - [Audio](https://en.wikipedia.org/wiki/Comparison_of_audio_coding_formats)

[Media container](https://en.wikipedia.org/wiki/Digital_container_format)

- Defines format to contain/wrap the encoded media (produced by codec)
- Popular [media containers](https://en.wikipedia.org/wiki/Comparison_of_video_container_formats)

