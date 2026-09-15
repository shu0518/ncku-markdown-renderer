# 2026 職業棒球選手體能與營養指南 

## 1. 專案簡介

本專案主題是一份以 2026 年職業棒球選手為對象的綜合指南，涵蓋週期化訓練框架、營養策略、以及結合穿戴裝置 API 的疲勞監測。

**選用的渲染工具及理由：**

* **Quarto**：負責將 Markdown 渲染為具備學術白皮書質感的 PDF。選用理由為其強大的底層擴充性，能完美支援 Mermaid 複雜流程圖、LaTeX 數學公式，並可透過傳遞變數精準控制排版與中文字體。
* **Marp CLI**：負責將同一份 Markdown 渲染為互動式網頁簡報與會議用 PDF。選用理由為其極致的輕量化與轉換速度，且支援注入客製化 CSS (`style.css`) 來解決長篇幅內容的版面溢位問題。

---

## 2. 環境需求


* **作業系統**：Windows 10 / 11 
* **所需語言 Runtime**：
    * **Node.js**：`v20.11.x` (LTS) - [官網下載](https://nodejs.org/)
    * **Quarto CLI**：`v1.4.550` - [官網下載](https://quarto.org/docs/get-started/)

* **系統層套件安裝指令**：
    * `quarto install tool tinytex` (安裝 LaTeX 引擎)
    * `npm install -g @marp-team/marp-cli` (安裝簡報轉換工具)


---

## 3. 安裝步驟

```powershell
# 步驟 A：前往官網下載並安裝
# 1.Node.js: https://nodejs.org/
# 2.Quarto: https://quarto.org/docs/get-started/

# 步驟 B：安裝與設定 (請使用管理員權限之 PowerShell/cmd)
# 解除執行限制
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned

# 安裝 PDF 渲染所需之 LaTeX 引擎 (TinyTeX)
quarto install tool tinytex

# 安裝 Marp CLI
npm install -g @marp-team/marp-cli

# 驗證安裝版本
node -v
quarto --version
marp --version
```

## 4. 執行渲染

```powershell

# 步驟一：Quarto 渲染 pdf (產出時會清空指定資料夾，故請先執行此指令)
quarto render content.md --to pdf --pdf-engine=xelatex -V CJKmainfont="Microsoft JhengHei" -V colorlinks=true -V fvextraopts="breaklines=true" -V fontsize=13pt --output output.pdf --output-dir output --lua-filter=remove-hr.lua

# 步驟二：Marp 渲染互動式 HTML 簡報
marp content.md --theme-set style.css --theme my-theme -o output/slides.html

# 步驟三：Marp 渲染靜態 PDF 簡報
marp content.md --theme-set style.css --theme my-theme --pdf -o output/slides.pdf
```

## 5. 預期輸出

執行完上述指令後，`output\` 資料夾內將會生成以下三個檔案：

1. **`final_report.pdf` (Quarto 產出)**
   * **格式**：A4 尺寸的專業 PDF 文件。
   * **樣貌**：具備乾淨的層級標題、自動換行的程式碼區塊、精美的 Mermaid 決策樹。原本 Markdown 中用於簡報分頁的 `---` 符號，已透過 Lua 過濾器在渲染時完美隱藏，不影響白皮書排版。
2. **`slides.html` (Marp 產出)**
   * **格式**：單頁式 HTML 網頁應用程式 (SPA)。
   * **樣貌**：可直接於瀏覽器開啟，並透過鍵盤流暢切換的互動式簡報。經客製化 CSS (`style.css`) 處理後，左右留白收窄，有效解決長內容溢位 (Overflow) 的問題。
3. **`slides.pdf` (Marp 產出)**
   * **格式**：16:9 橫式 PDF 簡報。
   * **樣貌**：固定排版的靜態簡報文件，適合做為會議發放之講義或供無瀏覽器環境預覽。

## 6. 參考資料

* [Quarto 官方文件 - PDF 渲染與變數設定](https://quarto.org/docs/output-formats/pdf-basics.html)
* [Pandoc Lua Filters 官方教學 (用於攔截並修改 AST)](https://pandoc.org/lua-filters.html)
* [Marp CLI 官方文件 (命令列操作指南)](https://github.com/marp-team/marp-cli)
* [Marp 客製化主題 CSS 指南 (Tweak default theme)](https://marpit.marp.app/theme-css)