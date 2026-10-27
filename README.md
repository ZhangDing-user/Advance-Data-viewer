# Advanced Data Viewer

**通用材料试验数据查看器（Windows，单文件免安装），带一个可以被 AI 直接驱动的 HTTP 接口。**

直接打开试验机的原始记录，不需要先导出成 CSV；也支持 `.csv` / `.tsv` / `.txt` / `.dat` 等通用文本数据文件。
最多 7 条互不干扰的 Y 轴叠加；自带派生通道表达式、A–B 区间测量、逐循环与分段统计、相位差与曲线拟合；
界面上的每一项能力都通过 28 个 HTTP 工具对外开放。程序内部刻意不做聊天窗口——它只负责把数据画清楚，
把接口开出来，说话的部分交给外部 AI 代理。

## 下载

最新版见本仓库的 **Releases** 页面。当前版本 `v1.10.13`，文件名
`Advanced Data Viewer_v1.10.13.exe`，46.8 MB。

SHA-256：`55b9607de59bd95abfd695494e326c4561fa8d3c7522de664878c8376532a299`

下载后可比对；一致即说明文件完整、未被篡改。

## 快速上手

1. 把 exe 放到任意目录——它是单文件，不需要旁边有任何其他文件。
2. 双击运行。会弹出一个黑色命令行窗口，**不要关**，关掉就等于退出程序。
3. 浏览器随后自动打开 `http://127.0.0.1:8770`。首次启动约 4~5 秒，是单文件解包运行库的正常现象。
4. 点文件名右侧的 `...` 选数据文件。数据目录可在同目录 `config.json` 的 `roots` 里改，
   或启动时加 `--data-root "D:\试验数据"`。

## 能做什么

- **直读原始记录**：试验机的原始文件不用先转成 CSV；实测 265 MB / 38 万行可以流式读入。
- **最多 7 条独立 Y 轴**：每条轴有自己的刻度、颜色和竖排轴名，左右分列，曲线多时自动变稀。
- **放大到采样间隔级**：窗口内至少保留 6 个原始点，没有固定倍数上限；抽稀方式随缩放自动切换，
  近看是真波形，远看不会退化成一条条竖刺。
- **派生通道表达式**：四则与乘方、`abs` `sqrt` `log` `exp`、`min` `max`、`diff`、`integ`、
  `lp` `hp`、**`lp0` `hp0`（零相位滤波，正反各滤一次）**、`movmean`、`shift`。
  判相位、判损耗必须用零相位版——单向滤波会带来 `−atan(f/fc)` 的假相位，
  fc=40 时 10 Hz 就少 14°，比材料本身的 tanδ 大一个量级。
- **A–B 区间测量**：每个通道给 min / max / 均值 / 峰峰 / RMS / σ / 过零 / 周期 / 频率 / 斜率 / 有效率。
- **逐循环与分段统计**：按时长、按周期、按给定边界、按文件里的段标记切分；任一项都能
  「存为通道」，生成的阶梯线可以继续参与表达式和统计。
- **相位差**：单点 DFT，用真实时间戳，所以对突发采样（爆发段 0.12 ms、停歇段 1 s）仍然正确；
  同时给出幅值比和正弦拟合优度 `r²`，`r²` 低就说明这个相位数没有意义。
- **曲线拟合**：线性趋势、2–6 阶多项式、单频正弦；可把拟合结果叠加成新通道，源数据不变。
- **导出**：PNG 服务端渲染（所见即所得）、CSV 按当前视图范围全分辨率导出。

## 交给 AI 驱动

接口是本地 HTTP，不联网、不需要 API Key：

```
GET  /api/tools             28 个工具的 OpenAI 兼容定义
GET  /api/state             当前文件、图形定义、视图范围、接管标志
POST /api/call              {"tool":"set_view","args":{"x0":600,"x1":1200}}
POST /api/command           {"text":"打开 sample_01，画第 1 通道对时间，缩到 600-1200 秒"}
```

一句话下指令的写法，外部代理可以自己接大模型，也可以自己写规则。接管成功时界面右上角显示「AI 接管中」。

## 说明

- **Windows 10 / 11（64 位）**。单文件，内置 Python / numpy / pandas / matplotlib，无外部依赖。
- 这个 exe **没有代码签名**，首次运行时 Windows SmartScreen 可能提示「已保护你的电脑」，
  点「更多信息 → 仍要运行」即可；想先确认文件没问题，比对上面的 SHA-256。
- 本仓库**只发布程序，不含任何试验数据**；源码暂未公开。保留所有权利，可自由下载与使用。

---

## English

A single-file Windows viewer for general materials test data, with an HTTP interface an AI agent can drive.

It opens the machine's native records directly (no export step), and also reads `.csv` / `.tsv` /
`.tsv` / `.txt` / `.dat`. Up to 7 independent Y axes in one plot; derived-channel expressions with
zero-phase filtering (`lp0` / `hp0`); A–B measurement, per-cycle and per-segment statistics that can be
saved back as channels; phase difference from a single-bin DFT on the real timestamps; curve fitting;
PNG and CSV export. All 28 capabilities are exposed over local HTTP, so an external agent can drive the
window — there is deliberately no chat box inside the program.

Version `v1.10.13`, 46.8 MB, Windows 10/11 64-bit, no installer, no dependencies.
The exe is not code-signed, so SmartScreen may warn on first run (More info → Run anyway).
Binaries only — no test data is published, and the source is not public.
