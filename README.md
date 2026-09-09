# PPT Translator — 发行版 / Downloads

PPT/PDF 多语言 AI 翻译排版工具（Windows GUI / CLI）。通过 OpenAI 兼容接口调用大模型，支持混合模式（可编辑文本优先）与纯视觉模式（图片页面 / PDF 扫描识别），译文以黑体 + 金色高亮回填到原图排版位置，并内置 AI 前后校对。

## 下载 / Download

请到页面顶部 **Releases** 下载最新版本，当前推荐：

- `v1.1.1`（2026-09-09，首个正式版）→ `PPT_Translator_v1.1.1.zip`

历史版本可在 Releases 页查看。

## 使用说明 / Guides（多语言）

- 中文使用说明：[使用说明.md](使用说明.md)
- 中文入门教程：[使用教程.md](使用教程.md)
- English Guide: [User_Guide_EN.md](User_Guide_EN.md)
- 한국어 가이드: [User_Guide_KO.md](User_Guide_KO.md)

指南中会介绍：如何找模型服务商、什么是 Base URL 与 API Key、如何选择模型、两种处理模式的区别、输入输出目录与拖拽、以及常见问题排查。

## 快速开始 / Quick Start

1. 解压 zip，双击 `PPT_Translator.exe`（无需安装）。
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