# FFmpeg 技能集合

这是一个基于 FFmpeg 的媒体处理技能集合，为 AI 助手提供实用的日常媒体处理命令。

[English](./README.md)

##  Install

```bash
npx skills add https://github.com/chunpu/ffmpeg-skills
```

## 功能概览

- **[ffmpeg-video-processing](./skills/ffmpeg-video-processing)** - 视频处理：格式转换、压缩、裁剪、拼接、速度控制、水印、特效、字幕等
- **[ffmpeg-audio-processing](./skills/ffmpeg-audio-processing)** - 音频处理：格式转换、压缩、裁剪、拼接、音量调整、去噪降噪、音高修正等
- **[ffmpeg-image-processing](./skills/ffmpeg-image-processing)** - 图片处理：格式转换、压缩、尺寸调整、裁剪、旋转、水印、特效等
- **[ffmpeg-install](./skills/ffmpeg-install)** - 安装与检查：跨平台安装指南、环境检查、验证安装

### 前置条件

在使用这些 skill 之前，需要先看看系统是否有 ffmpeg 命令。

## 技能特点

- **实用优先**：只收录最常用、最实用的命令
- **按频次排序**：功能按日常使用频率从高到低排列
- **可直接复制**：所有示例都经过验证，可直接复制使用
- **详细说明**：每个命令都有场景说明和参数解释

## 快速开始

### 视频处理示例

```bash
# 视频压缩（适合邮件/微信发送）
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset medium output.mp4

# 提取视频中的音频
ffmpeg -i video.mp4 -q:a 0 -map a audio.mp3
```

### 音频处理示例

```bash
# 音频格式转换
ffmpeg -i input.wav -q:a 0 output.mp3

# 音频降噪
ffmpeg -i input.mp3 -af "afftdn" output.mp3
```

### 图片处理示例

```bash
# 图片格式转换
ffmpeg -i input.png -q:v 2 output.jpg

# 图片尺寸调整
ffmpeg -i input.jpg -vf "scale=1920:-1" output.jpg
```

更多详细示例请查看各个技能的 SKILL.md 文件。

## License

MIT
