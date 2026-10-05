# PPT Translator — 发行版 / Downloads

PPT/PDF 多语言 AI 翻译排版工具（Windows GUI / CLI）。通过 OpenAI 兼容接口调用大模型，支持混合模式（可编辑文本优先）与纯视觉模式（图片页面 / PDF 扫描识别），译文以黑体 + 金色高亮回填到原文附近位置，并内置 AI 前后校对。

## 下载 / Download

请到页面顶部 **Releases** 下载最新版本，当前推荐：

- `v1.2.0`（2026-10-05，**10 月版**）
  - `PPT_Translator_Setup_v1.2.0.exe` —— **安装版**（推荐：免管理员、自带卸载，升级自动保留配置与历史记录）
  - `PPT_Translator_v1.2.0.zip` —— **免安装版**（解压即用，适合放 U 盘或不想安装的场景）

历史版本可在 Releases 页查看。

## 本版更新（v1.2.0 / 10 月版）

- **表格翻译**：表格不再被整表跳过。表格按「一格一条」提取、逐格判断；混合 / 文本模式下译文**就地写回原表格单元格**——边框、底纹、合并格、字号样式完全不变，输出就是「一张和原来一模一样的表」，只是文字换成目标语言（数字 / 序号保持原样，被替换的原文写入该页备注）。
- **排版优化**：两阶段背景估计（渐变背景也不干扰）、原文左右优先 + 坐标范数就近选位、译文框宽度贴合文字、按「从上到下、从左到右」排版，排版精细度可选（1/6 ~ 1/12）。
- **安装包**：免管理员、按用户安装、自带卸载程序；升级自动保留 `.env` / `cache` / `reports` / `logs` / `input` / `output` / `prompts`；从免安装 ZIP 迁移时可在向导里选择旧目录，一键继承 API Key、译文缓存与历史记录。
- **界面**：Windows 11 风格主题（跟随系统 / 浅色 / 深色）、历史记录窗口（任务记录 + 译文记忆，可搜索 / 导出）、字体与样式菜单（字体 / 字号 / 颜色 / 高亮色，三种模式通用）。
- **接口兼容**：OpenAI 官方 API、Anthropic Claude（Messages API），以及服务商自动识别（学校网关 Mindlogic / DeepSeek / 智谱 GLM / 阿里千问百炼 / Kimi）与任意 OpenAI 兼容接口；自动补全 Base URL、自动获取模型列表。
- **修复**：混合模式不再被 `.env` 的 `VISION_MODE` 强制成视觉模式；安装向导「继承旧版本配置」页勾选框与路径框联动；译文缓存损坏自动备份重建。

## 使用说明 / Guides（多语言）

- 中文使用说明：[使用说明.md](使用说明.md)
- 中文入门教程：[使用教程.md](使用教程.md)
- English Guide: [User_Guide_EN.md](User_Guide_EN.md)
- 한국어 가이드: [User_Guide_KO.md](User_Guide_KO.md)

指南中会介绍：如何找模型服务商、什么是 Base URL 与 API Key、如何选择模型、两种处理模式的区别、表格与排版规则、输入输出目录与拖拽、以及常见问题排查。

## 快速开始 / Quick Start

1. 安装版：双击 `PPT_Translator_Setup_v1.2.0.exe`，一路「下一步」；免安装版：解压后双击 `PPT_Translator.exe`。
2. 在界面中设置输入 / 输出目录，可直接把 PPTX / PPT / PDF 文件拖进窗口。
3. 填写 OpenAI 兼容的 Base URL 与 API Key（支持自动获取模型列表与测试连接）。
4. 选择处理模式：混合（文本优先，缺文本自动转视觉）或 纯视觉。
5. 点击开始，全部完成后自动打开输出目录。

- 环境要求：Windows 10/11 64 位；`.ppt` 转换与视觉渲染需要本机安装 Microsoft PowerPoint。
- 程序不会修改源文件，结果输出到所选输出目录。

## 反馈 / Feedback

欢迎把问题与建议发到 [Issues](https://github.com/Sanshui258/PPT-Translator-Releases/issues)。

## 许可与免责 / License & Disclaimer

个人学习 / 研究 / 教学 / 学术等**非商业用途**可用；禁止任何商业使用与滥用行为，作者保留追责权利，详见 [LICENSE](LICENSE)。

本项目为纯 Vibecoding（AI 辅助开发）产物，未经工程化优化与全面测试，请酌情使用；AI 翻译与排版输出可能存在错误，正式使用前请自行核对。