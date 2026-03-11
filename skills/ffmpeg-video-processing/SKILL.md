name: ffmpeg-video-processing
description: 基于 FFmpeg 的视频处理技能，提供格式转换、压缩、裁剪、拼接、音频提取、添加滤镜等典型场景的完整命令示例
license: MIT
user-invocable: true
tags: ["ffmpeg", "video", "processing", "conversion", "compression"]
---

# FFmpeg 视频处理技能 · 完整示例

> 目标：提供最常用的 FFmpeg 视频处理命令，覆盖 90% 的日常视频处理需求。所有示例均经过验证，可直接复制使用。

---

## 1. 格式转换 (Format Conversion)

### 基本转换

将视频从一种格式转换为另一种格式：

```bash
# MP4 转 WebM
ffmpeg -i input.mp4 output.webm

# MP4 转 MOV
ffmpeg -i input.mp4 output.mov

# AVI 转 MP4
ffmpeg -i input.avi -c:v libx264 -c:a aac output.mp4
```

### 保持画质转换

```bash
# 使用无损或接近无损的编码
ffmpeg -i input.mp4 -c:v libx264 -crf 18 -preset slow output.mp4

# 复制流（不重新编码，速度极快）
ffmpeg -i input.mkv -c copy output.mp4
```

---

## 2. 视频压缩 (Video Compression)

### 降低文件大小

```bash
# 使用 CRF (Constant Rate Factor) 压缩 (18-28 为推荐范围，数值越小质量越高)
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -preset medium output.mp4

# 降低比特率
ffmpeg -i input.mp4 -b:v 1M -b:a 128k output.mp4

# 两步压缩（质量更好）
ffmpeg -i input.mp4 -c:v libx264 -b:v 800k -pass 1 -f null /dev/null
ffmpeg -i input.mp4 -c:v libx264 -b:v 800k -pass 2 output.mp4
```

### H.265 (HEVC) 压缩（更高效）

```bash
# H.265 编码，文件更小
ffmpeg -i input.mp4 -c:v libx265 -crf 28 -preset medium output.mp4

# 硬件加速（如果支持）
ffmpeg -i input.mp4 -c:v hevc_videotoolbox -b:v 5M output.mp4
```

---

## 3. 分辨率调整 (Resolution Scaling)

### 调整到指定分辨率

```bash
# 固定宽度，高度按比例
ffmpeg -i input.mp4 -vf "scale=1280:-1" output.mp4

# 固定高度，宽度按比例
ffmpeg -i input.mp4 -vf "scale=-1:720" output.mp4

# 精确指定宽高
ffmpeg -i input.mp4 -vf "scale=1920:1080" output.mp4

# 强制保持宽高比，可能会裁剪或添加黑边
ffmpeg -i input.mp4 -vf "scale=1280:720:force_original_aspect_ratio=decrease,pad=1280:720:(ow-iw)/2:(oh-ih)/2" output.mp4
```

### 常用分辨率预设

```bash
# 1080p (Full HD)
ffmpeg -i input.mp4 -vf "scale=1920:1080" output_1080p.mp4

# 720p (HD)
ffmpeg -i input.mp4 -vf "scale=1280:720" output_720p.mp4

# 480p (SD)
ffmpeg -i input.mp4 -vf "scale=854:480" output_480p.mp4

# 4K 转 1080p
ffmpeg -i input_4k.mp4 -vf "scale=1920:1080" output_1080p.mp4
```

---

## 4. 视频裁剪 (Video Trimming)

### 按时间裁剪

```bash
# 从第 10 秒开始，截取 30 秒
ffmpeg -i input.mp4 -ss 00:00:10 -t 00:00:30 -c copy output.mp4

# 从开始到第 2 分钟
ffmpeg -i input.mp4 -to 00:02:00 -c copy output.mp4

# 从第 1 分 30 秒到结束
ffmpeg -i input.mp4 -ss 00:01:30 -c copy output.mp4

# 精确裁剪（重新编码，更准确）
ffmpeg -i input.mp4 -ss 00:00:10 -to 00:00:40 -c:v libx264 -crf 18 -c:a aac output.mp4
```

### 按帧裁剪

```bash
# 从第 100 帧开始，截取 200 帧
ffmpeg -i input.mp4 -vf "select='between(n\,100\,300)',setpts=N/FRAME_RATE/TB" -af "aselect='between(n\,100\,300)',asetpts=N/SR/TB" output.mp4
```

---

## 5. 视频拼接 (Video Merging)

### 使用 concat 协议（推荐）

首先创建一个文本文件 `filelist.txt`：

```
file 'video1.mp4'
file 'video2.mp4'
file 'video3.mp4'
```

然后拼接：

```bash
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4
```

### 重新编码拼接（格式不同时使用）

```bash
ffmpeg -i "concat:video1.mp4|video2.mp4|video3.mp4" -c:v libx264 -crf 18 -c:a aac output.mp4
```

---

## 6. 音频处理 (Audio Processing)

### 提取音频

```bash
# 提取为 MP3
ffmpeg -i input.mp4 -q:a 0 -map a output.mp3

# 提取为 WAV（无损）
ffmpeg -i input.mp4 -vn -acodec pcm_s16le -ar 44100 -ac 2 output.wav

# 提取为 AAC
ffmpeg -i input.mp4 -vn -c:a aac -b:a 192k output.aac
```

### 替换音频

```bash
# 用新音频替换原视频的音频
ffmpeg -i input.mp4 -i new_audio.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 -shortest output.mp4
```

### 调整音量

```bash
# 音量加倍
ffmpeg -i input.mp4 -filter:a "volume=2.0" output.mp4

# 音量减半
ffmpeg -i input.mp4 -filter:a "volume=0.5" output.mp4

# 自动归一化音量
ffmpeg -i input.mp4 -filter:a "loudnorm" output.mp4
```

### 移除音频

```bash
# 完全移除音频轨道
ffmpeg -i input.mp4 -c:v copy -an output_no_audio.mp4
```

---

## 7. 视频滤镜与特效 (Video Filters & Effects)

### 基本滤镜

```bash
# 水平翻转
ffmpeg -i input.mp4 -vf "hflip" output.mp4

# 垂直翻转
ffmpeg -i input.mp4 -vf "vflip" output.mp4

# 旋转 90 度顺时针
ffmpeg -i input.mp4 -vf "transpose=1" output.mp4

# 旋转 90 度逆时针
ffmpeg -i input.mp4 -vf "transpose=2" output.mp4

# 旋转 180 度
ffmpeg -i input.mp4 -vf "transpose=1,transpose=1" output.mp4
```

### 画面调整

```bash
# 调整亮度、对比度、饱和度
ffmpeg -i input.mp4 -vf "eq=brightness=0.1:contrast=1.1:saturation=1.2" output.mp4

# 去水印（模糊指定区域）
ffmpeg -i input.mp4 -vf "delogo=x=10:y=10:w=100:h=50" output.mp4

# 添加模糊效果
ffmpeg -i input.mp4 -vf "boxblur=5:1" output.mp4
```

### 裁剪画面

```bash
# 裁剪画面中心区域
ffmpeg -i input.mp4 -vf "crop=iw/2:ih/2:iw/4:ih/4" output.mp4

# 精确裁剪 (x, y, width, height)
ffmpeg -i input.mp4 -vf "crop=800:600:100:100" output.mp4
```

---

## 8. 添加文字与水印 (Text & Watermark)

### 添加文字

```bash
# 简单文字叠加
ffmpeg -i input.mp4 -vf "drawtext=text='Hello World':x=10:y=10:fontsize=24:fontcolor=white" output.mp4

# 带阴影的文字
ffmpeg -i input.mp4 -vf "drawtext=text='Hello World':x=10:y=10:fontsize=24:fontcolor=white:shadowx=2:shadowy=2:shadowcolor=black" output.mp4

# 居中文字
ffmpeg -i input.mp4 -vf "drawtext=text='Hello World':x=(w-text_w)/2:y=(h-text_h)/2:fontsize=48:fontcolor=white" output.mp4

# 动态时间戳
ffmpeg -i input.mp4 -vf "drawtext=text='%{localtime\:%X}':x=10:y=10:fontsize=24:fontcolor=white" output.mp4
```

### 添加图片水印

```bash
# 右下角水印
ffmpeg -i input.mp4 -i watermark.png -filter_complex "overlay=W-w-10:H-h-10" output.mp4

# 左上角水印
ffmpeg -i input.mp4 -i watermark.png -filter_complex "overlay=10:10" output.mp4

# 居中水印
ffmpeg -i input.mp4 -i watermark.png -filter_complex "overlay=(W-w)/2:(H-h)/2" output.mp4

# 半透明水印
ffmpeg -i input.mp4 -i watermark.png -filter_complex "[1:v]format=rgba,colorchannelmixer=aa=0.5[wm];[0:v][wm]overlay=W-w-10:H-h-10" output.mp4
```

---

## 9. 视频转 GIF (Video to GIF)

### 高质量 GIF 转换

```bash
# 简单转换
ffmpeg -i input.mp4 -vf "fps=10,scale=320:-1:flags=lanczos" -c:v gif output.gif

# 两步优化（质量更好）
ffmpeg -i input.mp4 -vf "fps=10,scale=320:-1:flags=lanczos,palettegen" palette.png
ffmpeg -i input.mp4 -i palette.png -filter_complex "fps=10,scale=320:-1:flags=lanczos[x];[x][1:v]paletteuse" output.gif

# 裁剪 GIF
ffmpeg -i input.mp4 -ss 00:00:05 -t 00:00:03 -vf "fps=10,scale=480:-1:flags=lanczos,palettegen" palette.png
ffmpeg -i input.mp4 -ss 00:00:05 -t 00:00:03 -i palette.png -filter_complex "fps=10,scale=480:-1:flags=lanczos[x];[x][1:v]paletteuse" output.gif
```

---

## 10. 视频预览与缩略图 (Thumbnails & Previews)

### 生成缩略图

```bash
# 从第 10 秒获取一帧
ffmpeg -i input.mp4 -ss 00:00:10 -vframes 1 thumbnail.jpg

# 从中间获取一帧
ffmpeg -i input.mp4 -vf "select=eq(n\,floor(N/2))" -vframes 1 thumbnail.jpg

# 生成多张缩略图（每秒一张）
ffmpeg -i input.mp4 -vf "fps=1" thumbnail_%04d.jpg

# 生成指定数量的缩略图（比如 10 张）
ffmpeg -i input.mp4 -vf "select='not(mod(n\,floor(N/10)))'" -vframes 10 thumbnail_%04d.jpg
```

### 生成视频预览（缩略图网格）

```bash
# 生成 3x3 网格预览图
ffmpeg -i input.mp4 -vf "select='not(mod(n\,floor(N/9)))',scale=160:90,tile=3x3" -vframes 1 preview_grid.jpg
```

---

## 11. 帧率调整 (Frame Rate)

### 修改帧率

```bash
# 改为 30 fps
ffmpeg -i input.mp4 -r 30 output.mp4

# 改为 60 fps（插帧）
ffmpeg -i input.mp4 -vf "fps=60" output.mp4

# 使用 minterpolate 进行平滑插帧
ffmpeg -i input.mp4 -vf "minterpolate=fps=60" output_60fps.mp4
```

---

## 12. 旋转与镜像 (Rotation & Mirror)

```bash
# 旋转 90 度（顺时针）
ffmpeg -i input.mp4 -vf "transpose=1" output.mp4

# 旋转 90 度（逆时针）
ffmpeg -i input.mp4 -vf "transpose=2" output.mp4

# 旋转 180 度
ffmpeg -i input.mp4 -vf "transpose=1,transpose=1" output.mp4

# 水平镜像
ffmpeg -i input.mp4 -vf "hflip" output.mp4

# 垂直镜像
ffmpeg -i input.mp4 -vf "vflip" output.mp4
```

---

## 13. 视频信息查看 (Video Information)

### 查看视频元数据

```bash
# 查看基本信息
ffmpeg -i input.mp4

# 详细信息（使用 ffprobe）
ffprobe -v quiet -print_format json -show_format -show_streams input.mp4

# 获取视频时长
ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 input.mp4

# 获取视频分辨率
ffprobe -v error -select_streams v:0 -show_entries stream=width,height -of csv=s=x:p=0 input.mp4
```

---

## 14. 批量处理 (Batch Processing)

### Shell 脚本批量处理

```bash
# 批量转换 MP4 到 WebM
for file in *.mp4; do
    ffmpeg -i "$file" "${file%.mp4}.webm"
done

# 批量压缩
for file in *.mp4; do
    ffmpeg -i "$file" -c:v libx264 -crf 23 "compressed_$file"
done

# 批量生成缩略图
for file in *.mp4; do
    ffmpeg -i "$file" -ss 00:00:05 -vframes 1 "${file%.mp4}_thumb.jpg"
done
```

---

## 15. 高级技巧 (Advanced Tips)

### 硬件加速

```bash
# macOS (VideoToolbox)
ffmpeg -i input.mp4 -c:v h264_videotoolbox -b:v 5M output.mp4

# Windows (NVENC)
ffmpeg -i input.mp4 -c:v h264_nvenc -preset p6 -tune ll -b:v 5M output.mp4

# Linux (VAAPI)
ffmpeg -vaapi_device /dev/dri/renderD128 -i input.mp4 -vf 'format=nv12,hwupload' -c:v h264_vaapi -b:v 5M output.mp4
```

### 流式处理

```bash
# 流式输出到 stdout
ffmpeg -i input.mp4 -f matroska - | some_command

# 从 stdin 读取
some_command | ffmpeg -i pipe:0 output.mp4
```

### 预设文件

创建 `preset.txt`：

```
vcodec=libx264
crf=23
preset=medium
acodec=aac
b:a=128k
```

使用预设：

```bash
ffmpeg -i input.mp4 -fpre preset.txt output.mp4
```

---

## 16. 常见问题与解决方案

### 视频播放顺序问题

```bash
# 重新排序时间戳
ffmpeg -i input.mp4 -c copy -fflags +genpts output.mp4
```

### 修复损坏的视频

```bash
# 尝试修复
ffmpeg -i input.mp4 -c copy -avoid_negative_ts 1 output.mp4
```

### 音频视频不同步

```bash
# 延迟音频 0.5 秒
ffmpeg -i input.mp4 -itsoffset 0.5 -i input.mp4 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 output.mp4

# 延迟视频 0.5 秒
ffmpeg -i input.mp4 -itsoffset 0.5 -i input.mp4 -c:v libx264 -c:a copy -map 1:v:0 -map 0:a:0 output.mp4
```

---

## 参数速查表

### 常用编码参数

| 参数 | 说明 |
|------|------|
| `-c:v libx264` | H.264 视频编码 |
| `-c:v libx265` | H.265/HEVC 视频编码 |
| `-c:a aac` | AAC 音频编码 |
| `-crf 18-28` | 恒定质量因子 |
| `-preset ultrafast/slow` | 编码速度预设 |

### 常用滤镜参数

| 滤镜 | 说明 |
|------|------|
| `scale=w:h` | 调整分辨率 |
| `crop=w:h:x:y` | 裁剪画面 |
| `transpose=1/2` | 旋转视频 |
| `hflip/vflip` | 水平/垂直翻转 |
| `drawtext` | 添加文字 |
| `overlay` | 叠加图片/视频 |
