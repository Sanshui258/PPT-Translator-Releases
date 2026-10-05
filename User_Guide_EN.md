# PPT Translator User Guide (Official v1.2.0)

> Welcome to the **first official release of PPT Translator** — a local PPT / PDF AI translation tool.
> It reads PPTX / PPT / PDF files, translates text (e.g. Korean / English) into Simplified Chinese (target language is configurable),
> lays the translation out at a sensible spot on the page, and exports a new `filename_zh.pptx`. **Your original files are never modified.**
>
> 💬 Questions, bug reports and feature ideas are welcome on GitHub Issues:
> **https://github.com/Sanshui258/PPT-Translator-Releases/issues**
> Please include the log output from the status window (or the newest `logs/` file) to help us diagnose.

---

---

## What's new in v1.2.0 (October release)

- **Table translation (new)**: tables are no longer dropped as a whole
  - tables are extracted cell by cell and filtering is per cell, so one cell that looks like code or
    a formula can no longer discard the entire table;
  - in Hybrid / Text mode the translation is written **back into the original table cells**: borders,
    fills, merged cells and font styling stay exactly as they were - the result is the same table,
    just in the target language;
  - numbers and serial numbers are left untouched, and replaced originals are saved to slide notes.
- **Layout keeps improving**: the background is estimated and removed first (gradients no longer
  confuse it); candidates prefer the left/right of the original, then above/below, and the closest
  non-overlapping blank block is picked by coordinate norm; box width now hugs the text; elements are
  placed top-to-bottom, left-to-right.
- **Installer**: a Windows setup (`PPT_Translator_Setup_v1.2.0.exe`) - no admin rights, per-user
  install, bundled uninstaller. Upgrading keeps `.env` / `cache` / `reports` / `logs` / `input` /
  `output` / `prompts`; when migrating from the portable ZIP the wizard can import your old folder
  (API key, translation memory and history) in one step.
- **Modern UI**: Windows 11 (Sun Valley) theme with system / light / dark switching.
- **History window**: review every run (time / files / result / mode / output path) and browse, search
  and export the translation memory.
- **Font & style menu**: font / size / text colour / highlight colour, shared by Hybrid, Vision and
  in-place replace; defaults follow the target language (EN -> SimHei, ZH -> SimSun, KO -> Malgun
  Gothic) at 12 pt; size strategy `fixed` or `ratio` (original x 7/16); code boxes only get their
  comments translated.
- **API compatibility**: OpenAI and Anthropic Claude (Messages API), plus automatic provider detection
  (Mindlogic campus gateway / DeepSeek / Zhipu GLM / Alibaba Qwen Bailian / Kimi) and any
  OpenAI-compatible endpoint; Base URL paths are completed automatically and the model list is fetched.
- **Other fixes**: output name conflict check (overwrite / rename / cancel), automatic archiving of
  inputs into `translated/`, drag & drop, opening the output folder when finished, Hybrid no longer
  forced into Vision by `VISION_MODE`, and the installer's import checkbox is now linked correctly.

## 1. What Is This & What It Does

In one line: it automates the annoying work of "reading text from images" and "copy-pasting text into a translator".

- **Inputs:** `.pptx` / `.ppt` / `.pdf` (Word, Excel, and other files are skipped automatically — a deliberate whitelist design).
- **Translation:** it prefers to read the **editable text** inside a PPT directly; for image-based slides or scanned PDFs it automatically switches to **vision-based extraction**.
- **Layout:** translations are placed near the original text (below → above → nearby blank space → a larger empty area of the page), avoiding overlap with the original, with font size auto-fitted so text stays readable.
- **Proofreading:** after translation, the AI checks the result against the original and auto-fixes omissions / truncation / errors, then re-lays-out if needed.
- **Large files:** context usage is tracked and batches are auto-compacted; decks over ~50 pages or a very large estimated token count are split into 2 parts, translated, then merged.

The UI supports **简体中文 / English / 한국어**, and progress / status / error messages follow the selected language.

---

## 2. Three Small Concepts (for first-time users)

### 2.1 What is an API Key?

An API Key is a **credential string issued by your model provider** (usually a long string like `sk-...`). It tells the provider who is calling and which account to bill — think of it as the password to the model service.

- **Where to get it:** register on the provider's website, open the **API Keys** page, and create a new key.
- **Important:** a key equals money. Do not screenshot it, share it, or commit it into public code. This tool stores your key only in the local `.env` file; the release build contains **no built-in keys**.

### 2.2 What is a Base URL?

The Base URL is the **gateway address of your model provider** — the entry point of its OpenAI-compatible API.

- Calling a model means sending an HTTP request to this URL that says "please translate this text".
- Every provider has a different address, usually published in its API documentation, e.g.:
  - Alibaba Cloud Bailian (Qwen): `https://dashscope.aliyuncs.com/compatible-mode/v1`
  - DeepSeek: `https://api.deepseek.com` (or `https://api.deepseek.com/v1`)
  - Zhipu AI (GLM): `https://open.bigmodel.cn/api/paas/v4`
  - Kimi (Moonshot): `https://api.moonshot.cn/v1`
  - School / enterprise gateways (e.g. Dongguk WISE / Mindlogic): follow the docs your school provides, usually something like `https://.../v1/gateway`.
- The tool knows several providers: when it recognizes one, it auto-normalizes the URL to the canonical path (e.g. appends `/v1/gateway`) — you rarely need to fiddle with it.

### 2.3 What is a Model / Model Name?

The model name selects which "AI brain" to use, e.g. `qwen-plus`, `qwen-vl-plus`, `gemini-3.5-flash`.

- **Text-only models:** good at translating words, but **cannot look at images**.
- **Multimodal models** (names often contain `vl`, `vision`, or a family name like `qwen-vl*`, `gemini-*`): they can read text **and** understand text/layout inside images.
- When to use which:
  - Translating only "directly readable PPT text" → a text model is enough.
  - Using **vision mode**, or letting the hybrid mode fall back to vision on image-based slides → you **must** pick a multimodal model, otherwise you will get `HTTP 400: Unexpected item type in content`.

> Quick memory: **Base URL = provider's front door, API Key = the door key, Model name = which brain you want.**
> All three are found in the provider console / docs; after filling them in, click **Get Models / Test Connection** to verify.

---

## 3. Finding a Provider & Setting Up (beginner path)

1. **Pick a provider:** Alibaba Cloud Bailian (Qwen), DeepSeek, Zhipu, or Kimi are common choices; students may also use a university gateway (e.g. Dongguk WISE / Mindlogic) — check your school's docs for how to apply.
2. **Register & enable:** go to the provider console → open **API Keys** → create a key and copy it.
3. **Find the Base URL and model name:** open the provider's **API documentation**, search for "Base URL" / "OpenAI compatible" / "models", and copy the address and the model name you want.
4. **Fill them into the tool:**
   - Run `PPT_Translator.exe` → in the "Model" section fill in `Base URL` and `API Key`.
   - Click **Get Models** to list available models (you can also type a name manually).
   - If fetching fails, type the model name first and click **Test Connection**.
5. **Choose the mode:** use **Hybrid** for normal PPTs; **Vision** for PDFs / scanned files; tick **Dry-run** if you first want to see how much would be translated.

---

## 4. Quick Start (five steps)

1. **Unzip** the release package anywhere (a path without spaces or special characters is recommended).
2. **Double-click `PPT_Translator.exe`** (switch the UI language in the top-right corner).
3. **Configure the model:** fill in `Base URL` + `API Key` + select a `Model` (see above).
4. **Choose mode & options:**
   - Processing mode: `Hybrid (text first, fall back to vision)` — recommended for PPT/PPTX;
     `Vision` (required for PDFs); `Dry-run` (checks only, costs no tokens).
   - Language direction: source languages `ko` (first) / `en` (second) → target `zh-CN` (changeable, e.g. Traditional Chinese).
   - Optional toggles: `AI proofread` (on by default), `Replace text in place (move original to notes)`, `Force re-translate`, and a `Page range`.
5. **Add files and start:** **drag & drop** PPT/PPTX/PDF files into the window (they are copied into the input folder automatically),
   or click **Browse** next to the input directory to select a whole folder → click **Start**.
   When finished, the **output folder opens automatically**; results are named `xxx_zh.pptx`.

Tip: the number next to the progress bar is `done/total`. You can press **Stop** anytime; finished files are kept and partial outputs are cleaned up.

---

## 5. Features in Detail

### 5.1 Two processing modes (Hybrid / Vision)
- **Hybrid:** first tries to read the editable text inside the PPT; if slides are pure images or scanned content, it automatically falls back to vision.
- **Vision:** each page is rendered to an image → a multimodal model identifies "which text needs translation, where it is, and roughly how large it is" → extracts & translates → lays out.
- **PDF rule:** when the input folder contains PDFs, hybrid mode is blocked and vision mode is required (a safeguard — PDFs have no editable text).
- **Text** (advanced): processes only directly readable text boxes; suitable when a deck is confirmed to be text-based.

### 5.2 Originals untouched, translation placed nearby (default)
Translations are added as new text boxes; the style is editable in the GUI "translation font" panel (font / size / color / highlight, shared by all modes):
- Default font follows the target language (EN → SimHei, ZH → SimSun, KO → Malgun Gothic); default size is a unified **12 pt**;
- Size policy `fixed` (default) / `ratio` (original × 7/16, floored); when text does not fit, the box grows first and only then shrinks;
- Placement uses five candidates (right / left / below / above the original + the lowest-activity HoG blank block), drops crowded ones, then picks the nearest by coordinate norm in top-to-bottom, left-to-right order.

### 5.3 In-place replace (optional, Hybrid / Text modes)
When enabled, the translation is **written back into the original text box** (keeping its original font size / color / layout), and the original text is saved into that slide's **notes (speaker notes)**. Useful when you want the Chinese text to simply replace the original in the same box. Tables are not supported yet — their text is kept as-is with a notice.

### 5.4 AI proofreading
After translation, the AI checks each item against the original (omissions, truncation, garbled text, terminology, naturalness in the target language) and auto-fixes + re-lays-out on errors. Enabled by default; uncheck it in the UI if you want it faster.

### 5.5 Context stats & auto-compaction
The UI shows cumulative call counts and token usage; when a request would exceed the context budget, content is automatically split into smaller batches to avoid "exceeds context window" errors.

### 5.6 Automatic splitting of large files
When the page count exceeds `SPLIT_MAX_PAGES` (default 50) or the estimated tokens exceed the limit, the file is split into 2 parts, translated separately, then merged into one complete PPTX (requires PowerPoint installed).

### 5.7 Languages & UI
- Language direction: `SOURCE_LANG_1 / SOURCE_LANG_2 → TARGET_LANGUAGE` — freely configurable (e.g. KO/EN → zh-CN, Traditional Chinese, Japanese, etc.).
- UI language: switch **中文 / English / 한국어** in the top-right corner; progress, status and error messages follow, no restart needed.
- Model lists and context stats update with the gateway; **Save Settings** writes back to the `.env` file next to the program.

### 5.8 Cache, re-translation & output
- Translated items are cached locally (`cache/`) so re-processing does not re-bill you; tick **Force re-translate** to ignore the cache.
- Output: `output/filename_zh.pptx`; reports in `reports/`; logs in `logs/`.

---

## 6. Troubleshooting (quick reference)

| Symptom | Cause & fix |
|---|---|
| Get models fails / 401 / 403 | Invalid / expired key or no permission: regenerate a key in the provider console and confirm the model is enabled. |
| 404 Not Found | Wrong Base URL: use the official address from the docs; gateways usually need `.../v1/gateway` (the tool also tries to auto-complete it). |
| SSL / network error / timeout | Domain unreachable or blocked by a proxy/firewall: open the Base URL in a browser first; for slow networks raise `AI_TIMEOUT_SEC` in `.env`. |
| `HTTP 400: Unexpected item type in content` | The selected model **does not support images**: for vision mode / hybrid fallback use a multimodal model (`qwen-vl*`, `gemini-*`, etc.). |
| "Folder contains PDF — vision mode required" | The input folder mixes PDFs: switch to **Vision** mode (safeguard design). |
| Large-file split fails (PowerPoint Clipboard error) | Splitting needs PowerPoint locally: install/update Office and retry, or translate in segments with a page range. |
| Translations pile up in a corner / look cramped | Increase `.env` `LAYOUT_NEAR_BLOCK_RADIUS` and `LAYOUT_MIN_BLOCK_RATIO` (e.g. 0.2), and set `LAYOUT_BLOCK_ORIGIN` to `top-right`. |
| Wrong UI language | Change it in the top-right language dropdown — no restart needed. |

> Still stuck? Post the **error line from the status window** or the newest content of `logs/` to
> **https://github.com/Sanshui258/PPT-Translator-Releases/issues**, including the mode, the Base URL (mask your key) and the page count.

---

## 7. Command-Line Version (optional, advanced)

The `PPT_Translator_CLI.exe` in the package supports headless batch processing:

```text
PPT_Translator_CLI.exe --file input\sample.pptx              # single file (hybrid by default)
PPT_Translator_CLI.exe --mode vision --force                 # vision mode, ignore cache
PPT_Translator_CLI.exe --mode hybrid --no-proofread          # disable AI proofreading
PPT_Translator_CLI.exe --inplace-replace --file a.pptx       # in-place replace (originals to notes)
PPT_Translator_CLI.exe --dry-run                             # check only, no API calls
```

Common flags: `--file`, `--input-dir`, `--output-dir`, `--pages 1-3`, `--mode auto|hybrid|text|vision`,
`--force`, `--dry-run`, `--proofread/--no-proofread`, `--inplace-replace/--no-inplace-replace`. Run `--help` for details.

---

## 8. Privacy & License

- **Privacy:** the program contains no built-in keys; your key is stored only in the local `.env`. The content of files you translate is sent to the model gateway you configure — check that provider's data policy yourself.
- **License:** distributed for personal / non-commercial use (see `LICENSE`). Commercial use and abuse are prohibited; the author reserves the right to take action.
- **Disclaimer:** AI translations may contain errors or omissions. Verify before publishing or using them formally.

> This project is a pure "Vibecoding" (AI-assisted improvisational development) artifact, not engineered or fully tested — use at your own discretion.
> This is the first official release. Thank you for trying it — **feedback goes to Issues: https://github.com/Sanshui258/PPT-Translator-Releases/issues**
