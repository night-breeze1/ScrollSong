# ScrollSong
生成歌词滚动视频程序

[生成歌词滚动视频介绍](https://blog.csdn.net/m0_62011148/article/details/163978466?spm=1001.2014.3001.5501)

# 纯前端+Python后端实现歌词滚动视频生成器，告别录屏时代

## 前言

做过音乐相关内容的人大概率遇到过这个场景：你需要一段歌词随音乐滚动播放的视频。传统做法是打开音乐播放器，按下录屏，等整首歌播完，再裁剪编辑。3 分钟的歌要等 3 分钟，10 分钟的专辑要等 10 分钟。

这个项目就是为了解决这个问题。传入音频和 LRC 歌词文件，直接生成歌词滚动播放的视频——不用录屏，不用等待实时播放。

项目提供两种生成模式：

- **浏览器录制**：纯前端 Canvas + MediaRecorder，开箱即用，无需安装任何环境
- **快速生成**：Python 后端 Pillow 逐帧渲染 + FFmpeg 合成，速度远快于实时，输出标准 MP4

## 项目效果

运行py文件，点击访问地址：http://127.0.0.1:5000
![项目启动](https://i-blog.csdnimg.cn/direct/355ac545a882415c8342c6a8b3548bd7.png)
界面左侧是控制面板，包含音频/歌词/封面上传、歌曲信息填写、视频格式选择、录制尺寸设置等。右侧是实时预览画布，模拟音乐播放器界面：黑胶唱片旋转、歌词平滑滚动、进度条可拖动。

预览界面完整复刻了音乐 App 的播放器风格——黑胶唱片中心可放置专辑封面，唱臂随播放进度移动，歌词区域当前行白色高亮，上下行灰色淡出。
选择音频文件和歌词文件
![音频歌词文件选择](https://i-blog.csdnimg.cn/direct/ddd8f2ec2dc94497ba11e53dfe90adeb.png)

点击预览可进行播放
音频格式MP4需要启动py后端
而webm可直接打开html即可进行浏览器录制
浏览器录制需要将歌曲完整播放完
![音频格式](https://i-blog.csdnimg.cn/direct/dfd4132e6daa4b15b6615a2868261536.png)
点击浏览器录制，等到进度条到100%，在该位置会显示生成的录制视频文件，可进行浏览播放和下载，webm格式文件较大，且进度条无法托拽，只能顺序播放
![浏览器录制](https://i-blog.csdnimg.cn/direct/26e0db60638a4bdca6013c10f7754808.png)
录制完成后点击下载即可秒下载完成
![webm格式视频](https://i-blog.csdnimg.cn/direct/db8cfac1bca4402b83ea3599cd0f8048.png)

还可录制mp4格式的视频，进度条可拖动（需要启动后端，点击访问地址：http://127.0.0.1:5000，可浏览器录制也可点击快速生成MP4）
![mp4视频](https://i-blog.csdnimg.cn/direct/3d22d675761148209335a78977ed2aa5.png)

## 完整代码

>通过网盘分享的文件：生成歌词滚动视频程序
>链接: [https://pan.baidu.com/s/1_EAXwz3myZGFEht-kkJhzQ?pwd=2jfj](https://pan.baidu.com/s/1_EAXwz3myZGFEht-kkJhzQ?pwd=2jfj) 提取码: 2jfj 
>--来自百度网盘超级会员v4的分享

## 环境搭建

### 前端（浏览器录制模式，零依赖）

用浏览器直接打开 `index.html` 即可使用浏览器录制功能。但注意：`AudioContext` 和 `MediaRecorder` 需要通过 `http://` 或 `https://` 访问才能正常工作，直接用 `file://` 协议打开可能受限。

如果只是体验浏览器录制，可以用 VS Code 的 Live Server 插件启动本地服务器。

### 后端（快速生成模式）

#### 1. 创建虚拟环境

```bash
cd lyric-video-generator

# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
# Windows:
venv\Scripts\activate
# Linux / macOS:
source venv/bin/activate
```

#### 2. 安装依赖

项目根目录下的 `requirements.txt` 列出了所有依赖：

```txt
Flask>=3.0
Pillow>=10.0
```

一键安装：

```bash
pip install -r requirements.txt
```

#### 3. 配置 FFmpeg

FFmpeg 有两种配置方式，任选其一：

- **方式一**：系统安装 FFmpeg 并添加到 PATH（推荐）
  - Windows: 从 [BtbN/FFmpeg-Builds](https://github.com/BtbN/FFmpeg-Builds/releases) 下载，解压后将 `bin` 目录添加到系统环境变量
  - macOS: `brew install ffmpeg`
  - Linux: `sudo apt install ffmpeg`

- **方式二**：将 FFmpeg 可执行文件放入项目 `ffmpeg/` 文件夹
  - 将 `ffmpeg.exe`、`ffprobe.exe`（Windows）或 `ffmpeg`、`ffprobe`（Linux/macOS）复制到 `lyric-video-generator/ffmpeg/` 目录下
  - 程序启动时先找系统 PATH，找不到自动回退到此目录

#### 4. 启动后端

```bash
python backend.py
```

看到以下输出说明启动成功：

```
[信息] ffmpeg 来源: 系统 PATH -> /usr/bin/ffmpeg
==================================================
歌词视频生成器后端
  静态文件: /path/to/lyric-video-generator
  访问地址: http://localhost:5000
==================================================
```

浏览器访问 `http://localhost:5000`，左侧面板的"快速生成"按钮变为可用状态。

## 使用教程

### 获取 LRC 歌词文件

LRC 歌词可以从以下途径获取：

1. **音乐播放器导出**：部分音乐播放器支持导出 LRC 格式歌词
2. **歌词网站下载**：搜索"歌名 LRC"可找到专门的歌词网站
3. **浏览器开发者工具抓取**：在音乐网站播放歌曲时，打开开发者工具 → Network 面板，筛选 `.lrc` 请求，找到歌词文件并下载
4. **手动编写**：按 `[mm:ss.xx]歌词内容` 格式逐行编写，项目也支持无时间戳的纯文本歌词（自动均匀分配）

### 生成视频

1. 点击"音频文件"选择 MP3 文件（文件名格式 `歌曲名-歌手名-专辑名` 可自动填充信息）
2. 点击"歌词文件"选择 `.lrc` 文件，或点击"或粘贴歌词文本"直接粘贴
3. 可选：选择专辑封面图片
4. 选择录制尺寸（竖屏/方形/横屏）和帧率
5. 选择生成方式：
   - **浏览器录制**：点击"浏览器录制"按钮，实时播放并录制，结束后自动生成视频
   - **快速生成**：点击"快速生成 (MP4)"按钮，后端离线渲染，进度条实时显示渲染进度
6. 生成完成后，预览区显示视频，点击"下载视频"保存

## 项目结构

```
lyric-video-generator/
├── index.html              # 前端页面
├── app.js                  # 前端核心逻辑（Canvas渲染 + MediaRecorder录制）
├── styles.css              # 界面样式
├── backend.py              # Python 后端（Flask + Pillow + FFmpeg）
├── requirements.txt        # Python 依赖清单
├── .vscode/
│   └── settings.json       # VS Code 解释器配置
└── ffmpeg/
    └── README.md           # FFmpeg 配置说明
```

### 前端文件说明

| 文件         | 职责                                                         |
| :----------- | :----------------------------------------------------------- |
| `index.html` | 页面结构：侧边栏表单 + 画布预览区 + 隐藏的录制画布和音频元素 |
| `app.js`     | 核心逻辑：LRC 解析、黑胶绘制、歌词渲染、双画布同步、MediaRecorder 录制、后端 API 交互 |
| `styles.css` | 深色主题界面样式，侧边栏布局，表单控件，弹窗，进度条         |

### 后端文件说明

`backend.py` 提供以下 API：

| 接口                      | 方法 | 功能                               |
| :------------------------ | :--- | :--------------------------------- |
| `/`                       | GET  | 返回前端页面                       |
| `/api/health`             | GET  | 健康检查，前端用于检测后端是否在线 |
| `/api/render`             | POST | 提交渲染任务，异步执行             |
| `/api/status/<task_id>`   | GET  | 查询渲染进度                       |
| `/api/download/<task_id>` | GET  | 下载生成的视频                     |
| `/api/remux`              | POST | 修复 MP4 音频编码（Opus→AAC）      |

## 踩过的坑

### WebM 视频进度条不可拖动

MediaRecorder 录制的 WebM 文件没有索引信息，播放器无法拖动进度条。这是因为 WebM 的索引在文件末尾，而 MediaRecorder 是流式写入，不会回写索引。解决方案：改用后端 FFmpeg 合成，加 `-movflags +faststart` 将索引前置到文件开头。

### MP4 录制后音频无法播放

Chrome 的 MediaRecorder 虽然支持 `video/mp4` MIME 类型，但音频实际编码为 Opus，而非 AAC。大多数播放器（尤其是 Windows 自带播放器）不支持 MP4 容器中的 Opus 音频。解决方案：录制后自动上传到后端，FFmpeg 将音频转为 AAC，视频流直接复制不重编码。

### AudioContext 的 MediaElementSource 只能创建一次

`createMediaElementSource` 对同一个 HTMLMediaElement 只能调用一次，重复调用会报错。必须在首次使用时创建并缓存，后续复用同一个实例。代码中通过 `ensureAudioContext` 方法保证只初始化一次。

### 多进程渲染的字体缓存

每个 worker 进程独立加载字体文件，避免进程间共享状态。通过模块级字典 `_font_cache` 在每个 worker 内部缓存已加载的字体对象，减少重复 I/O：

```python
_font_cache = {}

def get_font(path, size):
    """获取字体对象，带缓存避免重复加载"""
    key = (path, size)
    if key not in _font_cache:
        _font_cache[key] = ImageFont.truetype(path, size)
    return _font_cache[key]
```

