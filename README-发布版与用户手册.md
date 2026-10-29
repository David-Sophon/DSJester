# DSJester 1.0.4 · 发布说明、安装教程与用户手册

更新日期：2026-10-29。此文件放在压缩包外，可随下载链接一起提供给玩家。

## 一、下载哪个文件

| 文件 | 大小 | 是否必需 | 内容 |
|---|---|---|---|
| [DSJester-v1.0.4.zip](https://github.com/David-Sophon/DSJester/releases/download/v1.0.4/DSJester-v1.0.4.zip) | 约25.6 MB | 必需 | 主模组源码、安装工具、游戏内界面、宠物素材、涂装资源与说明 |
| [DSJester-TTS-Whale-1.0.0-EXPERIMENTAL.zip](https://github.com/David-Sophon/DSJester/releases/download/v1.0.4/DSJester-TTS-Whale-1.0.0-EXPERIMENTAL.zip) | 约242 MB | 可选 | 小鲸鱼社区参考音频、GPT-SoVITS v2基础权重、许可、安装校验与路径配置工具 |
| `README-发布版与用户手册.md` | 文本文档 | 建议阅读 | 本教程，解压前即可阅读 |
| 两个ZIP各自的 `.zip.sha256` | 文本摘要 | 建议保留 | 检查文件下载是否完整 |

**普通使用先下载主模组即可；角色语音不是必需功能。** 小鲸鱼模型包版本保持1.0.0，可搭配主模组1.0.4，无需因主模组升级重复下载。

以下内容没有包含在上述两个包中：

- Python解释器，以及语音识别所需依赖。
- Vosk、Whisper语音识别模型：按下面的安装命令单独下载。
- 完整GPT-SoVITS引擎、PyTorch/CUDA和中文BERT、HuBERT、G2PW：仅启用实验角色声音时另行准备。
- Jester原声、Jester派生训练权重、官方F-14模块文件、手册全文索引及个人API Key。

旧版、带`PREVIEW`字样的包是历史测试快照，当前正式发布使用上表中的`DSJester-v1.0.4.zip`。

## 二、它能做什么

DSJester给原版F-14添加一个能听自然语言的小鲸鱼后座。输入经过本地语音识别，再交给DeepSeek理解；允许的后座操作由受控工具执行，回复显示在游戏内文字框。也可直接输入文字。

主要功能包括：

- 调用原版Jester和已适配入口完成部分雷达、电台等后座操作。
- 查询可读取的状态，回答本机F-14手册相关问题。
- 游戏内二维小鲸鱼：听取、翻本子、欢呼、受力、空闲吃白饭和点击反应。
- 可选10海里内全局态势辅助，按距离调整自动报点频率。
- 可选起降/受击等事件智能回复和低频闲聊。
- 可修改键位、绑定外设，设置称呼、语气和回复长度。

使用Saved Games工具插件，继续使用原版F-14，不注册新飞机、不替换后座3D模型。原版Jester仍工作；默认静音并关闭其字幕，可在专用设置恢复。静音不会暂停Jester的自动操作。

## 三、运行前准备

1. Windows电脑，已安装DCS World和可正常使用的Heatblur F-14。
2. 安装可通过`python`命令调用的Python。建议使用本项目已验证的Python 3.12；模型包配置工具要求Python 3.11或更新版本。
3. 准备自己的DeepSeek API Key，以及访问该服务的网络。
4. 语音输入需麦克风；当前Vosk/Whisper识别配置使用CPU，核心对话不要求本机运行DeepSeek大模型。

在命令提示符执行：

```cmd
python --version
```

应显示Python版本；若提示找不到命令，先修好Python的安装/PATH，再继续。不要将Key写进公开帖子、录屏或问题截图。

## 四、首次安装：按顺序完成

### 第1步：退出DCS，解压主包

将`DSJester-v1.0.4.zip`解压到长期保留的位置，例如：

```text
E:\DCS工具\DSJester-v1.0.4\
    README.md
    VERSION
    dcs\
    tools\
    ui\
    voice\
```

后文“项目根目录”指包含`dcs`和`tools`等文件夹的这一层。**不要只把整个源码目录丢进DCS的飞机Mods目录。** 安装器会记录后端路径，安装后搬动源码文件夹，需要重新运行安装器。

### 第2步：安装基础语音输入

在项目根目录打开命令提示符，执行：

```cmd
tools\py.cmd tools\setup_speech.py
```

这会下载Vosk中文模型和依赖，并创建后台启动需要的`runtime\voice.json`。首次安装请先完成这一步，再按需加Whisper；单独运行Whisper下载脚本不会代替这份基础配置的创建。

安装后可先用Vosk跑通流程。需要尝试更自然的中文和中英混合术语时，任选下列命令：

```cmd
tools\py.cmd tools\setup_natural_speech.py --model base
tools\py.cmd tools\setup_natural_speech.py --model small
```

`base`较小；`small`可尝试改善中英术语，但不能保证缩写识别准确，速度还受CPU和录音长度影响。下载后要在DCS专用设置中选中对应识别器。默认下载镜像为`hf-mirror.com`，若需官方源，在命令后加`--mirror https://huggingface.co`。

下载和依赖安装需要网络；正常识别在本机执行。不要关闭下载窗口，等命令正常完成后再继续。

### 第3步：准备Saved Games目录

默认配置目录是：

```text
C:\Users\你的用户名\Saved Games\DCS\
```

先确认自己的DCS实际使用哪个目录，例如有些环境仍使用`DCS.openbeta`。以下默认使用`DCS`。

安装器要求`Scripts\Export.lua`已经存在。已有该文件时保留原内容，不要覆盖。若没有，可在PowerShell中运行下面命令：它只创建缺失的目录/文件。

```powershell
$dsSavedGames = Join-Path $env:USERPROFILE 'Saved Games\DCS'
$dsScripts = Join-Path $dsSavedGames 'Scripts'
New-Item -ItemType Directory -Path $dsScripts -Force | Out-Null
$dsExport = Join-Path $dsScripts 'Export.lua'
if (-not (Test-Path -LiteralPath $dsExport)) {
    New-Item -ItemType File -Path $dsExport | Out-Null
}
```

使用其他配置目录时先修改第一行的`DCS`名称。不要用复制空文件的方法覆盖SRS、DCS-BIOS等已有导出内容。

### 第4步：安装模组

在项目根目录双击`dcs\install.cmd`，或执行：

```cmd
dcs\install.cmd
```

其他Saved Games目录使用：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File tools\install_savedgames.ps1 -Action install -DcsSavedGames "C:\Users\你的用户名\Saved Games\DCS.openbeta"
```

正常安装会添加：

| 目标 | 用途 |
|---|---|
| `Mods\tech\DSJester` | 专用设置、原生按键定义、界面和素材 |
| `Scripts\DSJester` | 导出桥和控制目录 |
| `Scripts\Hooks\DSJesterGameGUI.lua` | 游戏内界面和任务生命周期 |
| `Scripts\Export.lua`中的项目标记段 | 加载桥，保留其他插件内容 |

安装完成后完全重启DCS，不能只退出重进任务。

### 第5步：设置API、识别器与麦克风

打开DCS“设置 → 专用 → DSJester”：

1. 开启模组、自动启动和文字显示。
2. 填入自己的API Key。
3. 选择已经安装的Vosk/Whisper识别器。
4. 选择正确麦克风。使用“Windows默认输入”时，先检查系统默认是不是Oculus等虚拟设备。
5. **快捷键方案选择“DCS控制设置 → DSJester（默认，可改键/外设）”。**
6. 声音先选“关闭：仅文字”，先验证输入和文字回复。

原生可改键方案是新安装默认值，旧配置升级可能保留旧选项。旧式Ctrl+Alt/Ctrl+Shift是固定后台热键模式，**不会读取DCS控制页里修改的绑定**。

### 第6步：绑定按键或外设

打开DCS“设置 → 控制”，从配置列表选择 **DSJester**，找到通话、交互、隐藏等动作，按DCS正常流程绑定键盘或摇杆按钮并保存。

这是独立工具的控制配置，不需要手动修改F-14官方键位文件。默认通话键为**左Ctrl + 左Alt + 空格**；S只是开发测试中玩家自行绑定的示例，不是所有用户的默认键。

## 五、快速上手

### 语音输入

1. 进入原版F-14任务，解除暂停，等待后端连接提示。
2. 按住通话键，说一句短指令。
3. 松开后自动识别并处理，**不需要再按确认键**。
4. 观察`Pilot:`显示的识别文字、处理中提示和DS回复；有操作时同时观察真实座舱结果。

初次可说：“切到RWS。”随后试“用TWS AUTO。”有合适目标时再试“锁定正前方目标。”

若`Pilot:`已经把TWS MAN听成TWS AUTO，问题在识别阶段，不能把DS后续回复当作听对。可换中文说法或用文字输入排除识别影响。

### 文字输入

按交互键打开文字输入，点击输入框，用中文输入法输入，按Enter或点击发送。发送/取消后关闭交互，回到飞行。不要在录音或上一条操作处理中反复发送同一句。

### 默认键位表

| 默认键位 | 功能 |
|---|---|
| 左Ctrl + 左Alt + Space | 按住说话，松开自动识别/发送 |
| 左Ctrl + 左Alt + Enter | 兼容的草稿确认入口，正常语音流程不需要 |
| 左Ctrl + 左Alt + Backspace | 取消录音、草稿或回复声音 |
| 左Ctrl + 左Alt + M | 开关交互/文字输入 |
| 左Ctrl + 左Alt + H | 显示/隐藏小鲸鱼 |
| 左Ctrl + 左Alt + PageUp / PageDown | 上一页/下一页文字 |

需要点击小鲸鱼时开启交互；平时可关闭交互，避免占用鼠标。尺寸和左右位置可在专用设置调整。

## 六、可以怎么问、怎么下令

| 目的 | 示例 |
|---|---|
| 雷达模式 | “切到RWS。”“用TWS AUTO。” |
| 前方截获 | “锁定正前方目标。”“解锁。” |
| 电台 | “把电台调到251.225 MHz，AM。” |
| 附近态势 | “报告十海里内的飞机。”“三海里内有谁？” |
| 学习系统 | “TWS AUTO和TWS MAN有什么区别？”“TID这个显示怎么用？” |

操作受机型、电源、目标条件和可读状态限制。教学问答不应顺手执行操作；不能读到的状态应说未知。回复不等于真实操作证据，必要时看仪表。

### 雷达使用边界

RWS/TWS AUTO有实机可用反馈；TWS MAN、PD SEARCH、PULSE SEARCH若原版Jester没有明确入口，本版不能保证切换。原版Jester还会继续自动工作，不会为了维持某模式持续争抢。

锁正前方复用Jester逻辑，不代表可以精确锁住任意口头指定目标；当前不提供完整AWG-9目标列表与可靠逐目标IFF。部分角度仍是暂定换算，不能把请求回执当真实天线到位。

### 态势辅助与自动报点

这是可选的全局对象导出辅助，**不是AWG-9雷达探测或真实IFF**。手动查询最多10海里；非同阵营/阵营未知目标可自动提示：

| 距离 | 单目标重复提示间隔 |
|---|---|
| 6～10海里 | 约60秒 |
| 3～6海里 | 约40秒 |
| 1～3海里 | 约20秒 |
| 1海里内 | 约10秒 |

边界归入更近一档。进入更近档位可提前提醒，但所有目标共用至少10秒间隔；忙碌时延后。服务器禁止对象导出或数据过期时不可用，不绕过限制，无数据不等于无飞机。自动报点仍待更多实机验收。

### 事件、小鲸鱼与闲聊

小鲸鱼会随录音、处理、飞行姿态和事件变化；空闲后会打盹/吃白饭，点击有不同反应。事件智能回复和低频闲聊默认关闭，需要额外请求API时再开启。

起飞回复可采用欢快语气；航母着舰只有收到可靠事件及明确挂索编号时才能报几线。Hook环境不一定提供详细世界事件，缺少数据时降级普通起降回复，不猜编号。命中、受击等同样依赖真实事件；受击不等于已知某个具体系统损坏。

## 七、手册问答的安装与使用

在项目根目录执行，按自己的安装位置修改路径：

```cmd
tools\py.cmd tools\index_f14_manual.py --f14-root "D:\DCS World\Mods\aircraft\F14"
```

只读取本机已安装的Heatblur手册建立索引，不调用模型API。索引在`runtime\manual\f14.sqlite3`，不随发布包分发。DCS大更新后可重建。

开启专用设置的手册检索，然后问系统原理或操作方法。每次只附少量相关片段，不发送整本手册；答复仍可能有错，关键操作以当前机型手册和真实仪表为准。

## 八、声音输出与可选TTS模型包

**角色语音未完善，存在无声、长延迟等已知问题。建议先用纯文字或Windows默认语音。** 1.0.4修复声音选择被忙碌/ASR错误阻断，以及关闭后旧Windows音频又启动的竞态；仍需实际声音测试。

| 方案 | 是否需要模型包 |
|---|---|
| 仅文字 | 不需要 |
| Windows默认语音 | 不需要，使用本机已有系统语音 |
| 本地小鲸鱼社区参考声线 | 需要本可选包，以及另备的GPT-SoVITS完整环境 |
| Jester声线 | 当前不提供面向发布的原声/训练模型包 |
| Kokoro女声 | 本可选包不包含；该模型未打包交付 |

小鲸鱼不是官方DeepSeek App朗读声音，也不是单独训练完成的专用模型，而是社区参考音频搭配GPT-SoVITS v2基础模型。附带上游许可和来源。

### 安装小鲸鱼可选包

1. 先完成主模组安装。准备GPT-SoVITS v2 Windows环境，当前适配其Python 3.9运行库布局；还需完整中文BERT、HuBERT、G2PW及Python依赖。
2. 将模型包解压到独立目录，找到其中`install_optional_tts.py`和`README.md`。
3. 在模型包目录打开命令提示符，用系统Python 3.11或更新版本执行：

```cmd
python install_optional_tts.py --project "E:\DCS工具\DSJester-v1.0.4" --toolkit "E:\你的GPT-SoVITS目录"
```

`--project`指主模组源码根目录，**不能指Saved Games中的Mods/tech目录**。默认仅检查模型摘要和路径，不写配置、不加载模型、不录音或播放。

4. 检查通过后，正常关闭DCS/后端，再添加`--install`执行安装：

```cmd
python install_optional_tts.py --project "E:\DCS工具\DSJester-v1.0.4" --toolkit "E:\你的GPT-SoVITS目录" --install
```

安装工具重建本机绝对路径、写入小鲸鱼配置，保留并备份旧配置，不自动修改API Key、麦克风、键位或声音选择。已有实验绑定其他引擎目录时会停止，应使用原引擎或独立主项目副本。

5. 完整重启后，在专用设置选“本地小鲸鱼”，按兼容性选CPU/GPU。GPU选项只影响兼容TTS后端，不会把Whisper识别切成GPU。

这不是完整免配置推理整合包；新机器依赖兼容与实际声音仍需验证。未测量统一的游戏内占用上限，不能保证所有显卡占用低于固定比例。

## 九、更新、迁移和卸载

更新前正常退出DCS与旧后端，并备份个人配置。解压新版本到固定目录，运行新版本`dcs\install.cmd`，再完全重启。

主包不带本机`runtime`，因此新目录不会自动带上原有依赖、识别模型和`voice.json`。可重新运行识别安装脚本；熟悉目录结构的用户也可从自己的旧项目复制对应依赖/模型与配置，并检查配置中的旧绝对路径。不要复制他人的Key或整份DCS配置。

若启用了角色声音，新目录中应重新运行可选包路径配置工具，`--project`改为新项目目录；TTS包本身无需重新下载。手册索引也可在新目录重建。

安装器检查自有文件归属/摘要，遇到人工修改的文件会停止。先备份比较，不强制覆盖。卸载运行项目中的：

```cmd
dcs\uninstall.cmd
```

本地模型与日志在源码项目`runtime`下，是否删除自行决定，删除前备份。使用链接/Junction安装时，通过安装器卸载，不递归删除链接目标。

## 十、常见故障

| 问题 | 排查顺序 |
|---|---|
| 修改键位后没反应 | 选择DSJester控制配置并保存；专用设置选原生方案；完全重启DCS。1.0.4修复了Input.Data未初始化和绑定刷新问题，原生改键已获用户实测确认 |
| 默认组合键无反应 | 检查左右Ctrl/Alt；默认是左侧；确认没有选旧式方案或仍在文字输入状态 |
| 有录音提示但识别为空 | 检查Windows输入音量条、虚拟麦克风、实际设备与所选设备；先说一句完整短句 |
| Pilot文字把术语听错 | 改用中文说法/文字输入，或尝试small；模型回复不代表识别正确 |
| 后端启动失败 | 检查Python命令、是否先运行setup_speech.py生成voice.json；查看runtime/backend.err.log |
| 提示Whisper未安装 | 下载所选base/small完整模型，或切回已安装的Vosk |
| 文字也不回复 | 检查API Key、网络、模组启用状态及后台日志 |
| 显示Mock/模拟回复 | 没有读取有效Key时可能进入模拟模式，核对配置但不要公开Key |
| 本地小鲸鱼无声/慢 | 已知未完善项，先关语音或用Windows默认语音；检查模型包是否缺少完整引擎 |
| 某雷达模式切不过去 | 本版受Jester接口限制，不能因回复成功就认定切换到位 |
| 查附近飞机没有数据 | 确认态势辅助和导出权限；无数据不等于无目标 |
| UI更新没有变化 | 核对使用的是1.0.4正式包，重跑安装并完全重启；只换源码或只重进任务不够 |
| 主页图标问题 | 本版已改为86×86；具体屏幕环境下的效果尚待单独确认 |

反馈时提供：版本、机型、发生时间、实际绑定、Pilot识别文字、界面提示与座舱结果。日志常见于项目`runtime`和Saved Games的`Logs`目录。上传前检查个人信息与凭据。

## 十一、费用、隐私和验证范围

语音识别在本机完成，正常流程不把原始麦克风音频提交给DeepSeek；API会收到识别文字、必要状态/上下文，以及手册问答的少量摘录。需要联网并产生对应API费用，不是完全离线助手。

宠物状态机、本地事件过滤不逐帧调用API。事件AI与闲聊可关闭，默认均关；闲聊有频率与小时预算。原版Jester和DS的自动逻辑不同，声音静音不等于关闭原版自动操作。

已获用户确认的原生改键修复不代表全部功能验收。自动报点、详细航母事件、更多后座按钮、全部机型/外设、VR/多屏、多人完整性和长时间边界仍需更多验证。角色语音不作为稳定功能交付。

项目为社区实验，与Eagle Dynamics、Heatblur、DeepSeek官方无隶属关系。请使用自己的合法模块和API账号，保留模型包中各项上游许可。

## 十二、给发布者的文件清单

对外提供以下文件即可：

```text
DSJester-v1.0.4.zip
DSJester-v1.0.4.zip.sha256
DSJester-TTS-Whale-1.0.0-EXPERIMENTAL.zip          可选下载
DSJester-TTS-Whale-1.0.0-EXPERIMENTAL.zip.sha256
README-发布版与用户手册.md
发布文件清单.txt
```

不要把开发机的`runtime`、训练目录、私人配置、API Key、旧预览包一起上传。主包是代码与运行素材包，TTS包是实验模型包，说明下载时把两者分开。

两份ZIP均有CRC与文件摘要校验。PowerShell可计算下载文件的SHA-256，与同名`.sha256`或`发布文件清单.txt`核对：

```powershell
Get-FileHash -LiteralPath '.\DSJester-v1.0.4.zip' -Algorithm SHA256
Get-FileHash -LiteralPath '.\DSJester-TTS-Whale-1.0.0-EXPERIMENTAL.zip' -Algorithm SHA256
```
