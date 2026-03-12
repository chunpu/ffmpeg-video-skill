# FFmpeg Skills Collection

A collection of FFmpeg-based media processing skills, providing practical daily media processing commands for AI assistants.

[中文版](./README.zh-CN.md)

## Install

```bash
npx skills add https://github.com/chunpu/ffmpeg-skills
```

## Features Overview

- **[ffmpeg-video-processing](./skills/ffmpeg-video-processing)** - Video Processing: Format conversion, compression, cropping, merging, speed control, watermark, effects, subtitles, etc.
- **[ffmpeg-audio-processing](./skills/ffmpeg-audio-processing)** - Audio Processing: Format conversion, compression, trimming, merging, volume adjustment, noise reduction, pitch correction, etc.
- **[ffmpeg-image-processing](./skills/ffmpeg-image-processing)** - Image Processing: Format conversion, compression, resizing, cropping, rotation, watermark, effects, etc.
- **[ffmpeg-install](./skills/ffmpeg-install)** - Installation & Check: Cross-platform installation guide, environment check, installation verification

### Prerequisites

Before using these skills, check if your system has the ffmpeg command available.

## Skill Features

- **Practicality First**: Only includes the most commonly used and practical commands
- **Sorted by Frequency**: Features are ordered from most frequently used to least
- **Ready to Copy**: All examples are verified and ready to use
- **Detailed Explanations**: Each command includes scenario descriptions and parameter explanations

## Quick Start

### Video Processing Examples

```bash
# Video compression (suitable for email/WeChat sharing)
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset medium output.mp4

# Extract audio from video
ffmpeg -i video.mp4 -q:a 0 -map a audio.mp3
```

### Audio Processing Examples

```bash
# Audio format conversion
ffmpeg -i input.wav -q:a 0 output.mp3

# Audio noise reduction
ffmpeg -i input.mp3 -af "afftdn" output.mp3
```

### Image Processing Examples

```bash
# Image format conversion
ffmpeg -i input.png -q:v 2 output.jpg

# Image resizing
ffmpeg -i input.jpg -vf "scale=1920:-1" output.jpg
```

See each skill's SKILL.md file for more detailed examples.

## License

MIT
