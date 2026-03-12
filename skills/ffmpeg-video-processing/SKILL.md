name: ffmpeg-video-processing
description: 基于 FFmpeg 的视频处理技能，提供最实用的日常视频处理命令示例
license: MIT
user-invocable: true
tags: ["ffmpeg", "video", "processing", "conversion", "compression"]
---

# FFmpeg 视频处理技能 · 实用场景示例

> 目标：提供最常用、最实用的 FFmpeg 视频处理命令，覆盖 90% 的日常需求。所有示例均经过验证，可直接复制使用。

---

## 1. 视频转码（全设备兼容）

```bash
# MKV 转 MP4（H.264 + AAC，所有设备都能播放）
ffmpeg -i input.mkv -c:v libx264 -c:a aac output.mp4

# AVI 转 MP4（兼容性最好）
ffmpeg -i input.avi -c:v libx264 -c:a aac output.mp4

# WebM 转 MP4
ffmpeg -i input.webm -c:v libx264 -c:a aac output.mp4

# MOV 转 MP4
ffmpeg -i input.mov -c:v libx264 -c:a aac output.mp4
```

---

## 2. 视频压缩

### 压缩到 10MB 以下（邮件/微信发送）

```bash
# 1080p 视频压缩（质量优先）
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset medium output.mp4

# 720p 视频压缩（平衡大小和质量）
ffmpeg -i input.mp4 -vf "scale=1280:720" -c:v libx264 -crf 26 -preset medium output_720p.mp4

# 480p 视频压缩（最小体积）
ffmpeg -i input.mp4 -vf "scale=854:480" -c:v libx264 -crf 28 -preset medium output_480p.mp4
```

### 压缩为 H.265（文件更小）

```bash
# H.265 编码，文件比 H.264 小 30-50%
ffmpeg -i input.mp4 -c:v libx265 -crf 28 -preset medium output_h265.mp4
```

---

## 3. 视频裁剪

### 去掉片头片尾

```bash
# 从第 5 秒开始，截取 30 秒（去掉前 5 秒和后面的内容）
ffmpeg -i input.mp4 -ss 00:00:05 -t 00:00:30 -c copy output.mp4

# 从开始到第 2 分钟（只保留前 2 分钟）
ffmpeg -i input.mp4 -to 00:02:00 -c copy output.mp4

# 从第 1 分 30 秒到结束（去掉前 1 分 30 秒）
ffmpeg -i input.mp4 -ss 00:01:30 -c copy output.mp4

# 精确裁剪（重新编码，更准确）
ffmpeg -i input.mp4 -ss 00:00:10 -to 00:00:40 -c:v libx264 -crf 18 -c:a aac output.mp4
```

---

## 4. 视频拼接

### 合并多个视频片段

首先创建 `filelist.txt`：

```
file 'video1.mp4'
file 'video2.mp4'
file 'video3.mp4'
```

然后拼接：

```bash
# 快速拼接（不重新编码，格式必须一致）
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4

# 重新编码拼接（格式不同时使用）
ffmpeg -f concat -safe 0 -i filelist.txt -c:v libx264 -crf 18 -c:a aac output.mp4
```

---

## 5. 提取音频

### 做手机铃声/背景音乐

```bash
# 提取为 MP3（高质量）
ffmpeg -i input.mp4 -q:a 0 -map a output.mp3

# 提取为 MP3（192kbps）
ffmpeg -i input.mp4 -vn -c:a libmp3lame -b:a 192k output.mp3

# 提取为 WAV（无损，适合编辑）
ffmpeg -i input.mp4 -vn -acodec pcm_s16le -ar 44100 -ac 2 output.wav

# 提取为 AAC
ffmpeg -i input.mp4 -vn -c:a aac -b:a 192k output.aac
```

---

## 6. 添加水印

### 版权保护/品牌标识

```bash
# 右下角添加图片水印
ffmpeg -i input.mp4 -i logo.png -filter_complex "overlay=W-w-10:H-h-10" output.mp4

# 左上角添加图片水印
ffmpeg -i input.mp4 -i logo.png -filter_complex "overlay=10:10" output.mp4

# 居中添加图片水印
ffmpeg -i input.mp4 -i logo.png -filter_complex "overlay=(W-w)/2:(H-h)/2" output.mp4

# 半透明水印
ffmpeg -i input.mp4 -i logo.png -filter_complex "[1:v]format=rgba,colorchannelmixer=aa=0.5[wm];[0:v][wm]overlay=W-w-10:H-h-10" output.mp4

# 添加文字水印
ffmpeg -i input.mp4 -vf "drawtext=text='Your Name':x=10:y=10:fontsize=24:fontcolor=white" output.mp4
```

---

## 7. 视频转 GIF

### 做表情包/动图

```bash
# 简单转换（适合小视频）
ffmpeg -i input.mp4 -vf "fps=10,scale=480:-1:flags=lanczos" -c:v gif output.gif

# 高质量转换（两步法，推荐）
ffmpeg -i input.mp4 -vf "fps=10,scale=480:-1:flags=lanczos,palettegen" palette.png
ffmpeg -i input.mp4 -i palette.png -filter_complex "fps=10,scale=480:-1:flags=lanczos[x];[x][1:v]paletteuse" output.gif

# 裁剪视频片段转 GIF（从第 5 秒开始，截取 3 秒）
ffmpeg -i input.mp4 -ss 00:00:05 -t 00:00:03 -vf "fps=10,scale=480:-1:flags=lanczos,palettegen" palette.png
ffmpeg -i input.mp4 -ss 00:00:05 -t 00:00:03 -i palette.png -filter_complex "fps=10,scale=480:-1:flags=lanczos[x];[x][1:v]paletteuse" output.gif
```

---

## 8. 生成缩略图

### 视频预览/封面

```bash
# 从第 5 秒获取一帧作为缩略图
ffmpeg -i input.mp4 -ss 00:00:05 -vframes 1 thumbnail.jpg

# 从视频中间获取一帧
ffmpeg -i input.mp4 -vf "select=eq(n\,floor(N/2))" -vframes 1 thumbnail.jpg

# 生成 10 张缩略图（均匀分布）
ffmpeg -i input.mp4 -vf "select='not(mod(n\,floor(N/10)))'" -vframes 10 thumbnail_%04d.jpg

# 生成 3x3 网格预览图
ffmpeg -i input.mp4 -vf "select='not(mod(n\,floor(N/9)))',scale=160:90,tile=3x3" -vframes 1 preview_grid.jpg
```

---

## 9. 旋转/翻转视频

### 手机拍摄方向不对

```bash
# 顺时针旋转 90 度
ffmpeg -i input.mp4 -vf "transpose=1" output.mp4

# 逆时针旋转 90 度
ffmpeg -i input.mp4 -vf "transpose=2" output.mp4

# 旋转 180 度
ffmpeg -i input.mp4 -vf "transpose=1,transpose=1" output.mp4

# 水平镜像翻转
ffmpeg -i input.mp4 -vf "hflip" output.mp4

# 垂直镜像翻转
ffmpeg -i input.mp4 -vf "vflip" output.mp4
```

---

## 10. 调整音量

### 声音太小/太大

```bash
# 音量加倍
ffmpeg -i input.mp4 -filter:a "volume=2.0" output.mp4

# 音量减半
ffmpeg -i input.mp4 -filter:a "volume=0.5" output.mp4

# 音量增加 10dB
ffmpeg -i input.mp4 -filter:a "volume=10dB" output.mp4

# 自动归一化音量（让声音大小一致）
ffmpeg -i input.mp4 -filter:a "loudnorm" output.mp4
```

---

## 11. 移除音频

### 制作无声视频

```bash
# 完全移除音频轨道
ffmpeg -i input.mp4 -c:v copy -an output_no_audio.mp4
```

---

## 12. 替换音频

### 给视频换背景音乐

```bash
# 用新音频替换原视频的音频
ffmpeg -i input.mp4 -i new_audio.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 -shortest output.mp4
```

---

## 13. 分辨率调整

### 适配不同屏幕

```bash
# 4K 转 1080p
ffmpeg -i input_4k.mp4 -vf "scale=1920:1080" output_1080p.mp4

# 1080p 转 720p
ffmpeg -i input.mp4 -vf "scale=1280:720" output_720p.mp4

# 1080p 转 480p
ffmpeg -i input.mp4 -vf "scale=854:480" output_480p.mp4

# 固定宽度，高度按比例（宽度 1280）
ffmpeg -i input.mp4 -vf "scale=1280:-1" output.mp4

# 固定高度，宽度按比例（高度 720）
ffmpeg -i input.mp4 -vf "scale=-1:720" output.mp4
```

---

## 14. 帧率调整

### 慢动作/快动作

```bash
# 改为 30 fps
ffmpeg -i input.mp4 -r 30 output.mp4

# 改为 60 fps（插帧，更流畅）
ffmpeg -i input.mp4 -vf "minterpolate=fps=60" output_60fps.mp4
```

---

## 15. 批量处理

### 多个视频一起处理

```bash
# 批量转换为 MP4
for file in *.mkv *.avi *.webm *.mov; do
    if [ -f "$file" ]; then
        ffmpeg -i "$file" -c:v libx264 -c:a aac "${file%.*}.mp4"
    fi
done

# 批量压缩
for file in *.mp4; do
    ffmpeg -i "$file" -c:v libx264 -crf 28 -preset medium "compressed_$file"
done

# 批量生成缩略图
for file in *.mp4; do
    ffmpeg -i "$file" -ss 00:00:05 -vframes 1 "${file%.mp4}_thumb.jpg"
done
```

---

## 16. 查看视频信息

### 了解视频参数

```bash
# 查看基本信息（推荐，日常用这个就够了）
ffmpeg -i input.mp4
```

---

## 17. 常见问题修复

### 音视频不同步

```bash
# 重新排序时间戳
ffmpeg -i input.mp4 -c copy -fflags +genpts output.mp4
```

### 修复损坏的视频

```bash
# 尝试修复
ffmpeg -i input.mp4 -c copy -avoid_negative_ts 1 output.mp4
```

---

## 参数速查表

### 常用编码参数

| 参数 | 说明 |
|------|------|
| `-c:v libx264` | H.264 视频编码（兼容性最好） |
| `-c:v libx265` | H.265/HEVC 视频编码（文件更小） |
| `-c:a aac` | AAC 音频编码 |
| `-crf 18-28` | 恒定质量因子（18=最好，28=较小） |
| `-preset medium` | 编码速度预设 |
| `-c copy` | 直接复制流，不重新编码 |

### 常用滤镜参数

| 滤镜 | 说明 |
|------|------|
| `scale=w:h` | 调整分辨率 |
| `crop=w:h:x:y` | 裁剪画面 |
| `transpose=1/2` | 旋转视频 |
| `hflip/vflip` | 水平/垂直翻转 |
| `drawtext` | 添加文字 |
| `overlay` | 叠加图片/视频 |
