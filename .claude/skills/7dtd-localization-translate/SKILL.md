---
name: 7dtd-localization-translate
description: >-
  Translates 7 Days to Die mod Localization.txt files to Traditional Chinese
  (tchinese). Use when the user asks to translate Localization.txt,
  localization.txt, add tchinese column, work on Rebirth mod TC translation for
  a specified file or scope, or batch-translate the next N mods listed in
  TRANSLATION_LOG.md.
---

# 7DTD Rebirth Localization 繁中翻譯

將模組 `Config/Localization.txt`（或 `localization.txt`）的英文欄位翻譯為繁體中文，並寫入 `tchinese` 欄位。

格式細節對照見同目錄 `formats.md`。

## 觸發與範圍

依使用者指定處理：

| 指定方式 | 行為 |
|----------|------|
| 單一檔案路徑 | 只處理該檔 |
| 模組資料夾名稱 | 處理 `{資料夾}/Config/Localization.txt` |
| 「翻譯接下來 N 個」 / 「依序翻譯 N 個」 | 進入**批次模式**（見下方），自 `TRANSLATION_LOG.md`「尚未翻譯」清單由上而下取 N 個 |
| 多檔 / 批次 | 先彙整各檔確認表，**整批須待使用者回覆允許後**才開始寫入；不得因批次而跳過確認 |

檔名大小寫不拘（`Localization.txt` / `localization.txt`）。

---

## 批次模式（依 Log 依序翻譯指定數量）

當使用者要求「翻譯接下來 N 個模組」「依序翻譯 N 個」之類：

1. 讀取 `TRANSLATION_LOG.md`，取得「**尚未翻譯**」清單（必要時也可由「已有翻譯尚未檢查」取，須使用者指明）。
2. 由清單**由上而下**取前 N 個（N 為使用者指定數量）。
3. 對這 N 個檔各執行步驟 1（讀檔、記錄列數與格式）。
4. **彙整為單一確認表（多列，每檔一列）**，於步驟 2 一次回報，然後**停止等待使用者允許**。
5. 獲允許後，**逐檔**執行步驟 3～5（翻譯、寫入、更新 log），全部完成後再一次回報結果。
6. 每完成一檔即更新 `TRANSLATION_LOG.md`，避免中斷時狀態不一致。

批次模式同樣**不得跳過確認表**，也不得在確認表的同一輪回覆中直接開始寫入。

---

## 工作流程（必須依序執行）

**硬性規則：步驟 2 與步驟 3～5 不得在同一輪對話中連續執行。**

```
進度：
- [ ] 1. 解析範圍並讀取檔案
- [ ] 2. 分析格式與列數 → 輸出確認表 → 【停止，等待使用者回覆】
- [ ] 3. 新增或補齊 tchinese 欄位      ← 僅在步驟 2 獲允許後
- [ ] 4. 翻譯並填入                    ← 僅在步驟 2 獲允許後
- [ ] 5. 驗證並更新 TRANSLATION_LOG.md ← 僅在步驟 2 獲允許後
```

### 步驟 1：讀取檔案

1. 讀取目標 `Localization.txt` 全文。
2. 記錄：相對路徑、標題列、資料列數、是否已有 `tchinese` 欄位。
3. **列數定義**：
   - **總列數**：檔案中非空行數（含標題列）
   - **待翻譯列數**：需填入 `tchinese` 的資料列（排除標題；`NoTranslate` 為 `x` 的列可標記為跳過）

### 步驟 2：向使用者確認（必須停止並等待）

**此步驟完成後必須結束本輪回覆，不得在同一則回覆中繼續步驟 3～5。**

**在取得使用者對確認表的明確同意前，禁止：**
- 修改任何 `Localization.txt`
- 更新 `TRANSLATION_LOG.md`
- 輸出完整譯文預覽（僅可說明列數與格式）

**以下情形不視為已獲允許，仍須先輸出確認表並等待下一則回覆：**
- 使用者僅說「翻譯某某檔案」（指定範圍 ≠ 允許寫入）
- 使用者說「請開始翻譯」（意圖未確認，須先回報列數）
- 使用者一次指定多個檔案或要求批次 N 個（須先彙整確認表，再等允許）

**允許寫入的有效回覆範例：**「可以」、「開始」、「確認」、「OK」——須出現在**確認表之後的下一則訊息**。

單檔回報格式：

```markdown
## 翻譯前確認

| 項目 | 內容 |
|------|------|
| 檔案路徑 | `{路徑}` |
| 總列數 | {N}（標題 1 列 + 資料 {M} 列） |
| 標題欄位 | `{標題列摘要}` |
| 英文欄位 | `{欄位名稱}`（第 {index} 欄，0-based） |
| 已有 tchinese | 是 / 否 |
| 待翻譯列數 | {K} |
| 備註 | {格式特殊處、NoTranslate 列數等} |

請確認是否開始翻譯。回覆「可以」、「開始」或類似允許後，我才會進行翻譯並寫入檔案。
```

多檔 / 批次時，每檔一列彙整為單一表格，結尾同樣要求使用者回覆允許。

**確認表輸出後，本輪對話到此為止。**

### 步驟 3：新增 tchinese 欄位

依檔案既有格式處理，**不可破壞欄位順序、引號或逗號結構**。

#### 判斷英文來源欄

優先順序（欄名不分大小寫）：

1. `english`
2. `English`
3. 標題列中第一個語言內容欄（`Key` 之後、非 metadata 欄）

常見 metadata 欄：`File`, `Type`, `UsedInMainMenu`, `NoTranslate`, `Source`, `Context`, `Changes`

#### 新增欄位規則

| 情況 | 作法 |
|------|------|
| 標題列**無** `tchinese` | 依格式插入欄位名 `tchinese`（見下方） |
| 標題列**有** `tchinese` 但內容為空 | 不新增欄位，直接填入翻譯 |
| 標題列**有** `tchinese` 且已有內容 | 詢問使用者要覆寫、僅補空欄，或跳過 |

#### 插入位置（無 tchinese 時）

| 格式 | 標題列 | 資料列 |
|------|--------|--------|
| 精簡 `Key,english` | 末尾加 `,tchinese` | 末尾加 `,{譯文}` |
| 精簡 `Key,English,...` | 末尾加 `,tchinese` | 末尾加 `,{譯文}` |
| 完整 TFP（含 `schinese`） | 在 `schinese` 與 `spanish` 之間插入 `tchinese` | 在對應欄位位置插入譯文 |
| 完整 TFP（無 `schinese`） | 在 `english` 相關語系欄之後、或末尾加 `tchinese` | 對齊標題欄位數 |
| 其他多語欄 | 在**最後一個語系欄**之後加 `tchinese` | 每列補上對應空欄後填譯文 |

#### CSV 注意事項

- 欄位若含逗號、引號，須維持雙引號包裹（`"text, with comma"`）。
- 保留既有空白行；勿刪除註解行（若有）。
- `NoTranslate` 欄為 `x` 的列：`tchinese` 留空，不強制翻譯。
- 檔案編碼維持 UTF-8（含 BOM 則保留 BOM）。

### 步驟 4：翻譯並填入

以**英文欄位內容**為翻譯來源，產出繁體中文寫入 `tchinese`。

#### 翻譯原則

1. **語言**：繁體中文（台灣用語優先）；遊戲 UI 用語簡潔。
2. **保留不譯**：
   - 顏色 / 格式標記：`[e38d23]`、`(MS)`、`[-]` 等
   - 佔位符：`{0}`、`{1}`、`%s`、`%d`
   - 鍵名、檔名、路徑、純代碼
   - 已為中文的 `schinese` 欄**不可**直接複製；仍須依英文重新譯為繁中
3. **語境**：喪屍生存、工藝、武器、護甲、UI、設定選單。
4. **一致性**：同一檔案內相同英文片語使用相同譯法。
5. **空英文**：`tchinese` 留空。

#### 常見譯法參考

| English | 繁中 |
|---------|------|
| General | 一般 |
| Debug | 除錯 |
| Settings | 設定 |
| Tooltip | 工具提示 |
| Armor | 護甲 |
| Weapon | 武器 |
| Lock | 鎖 |
| Drone | 無人機 |

### 步驟 5：驗證與紀錄

寫檔後檢查：

- [ ] 標題列含 `tchinese`
- [ ] 每列欄位數與標題一致
- [ ] 引號欄位未破壞
- [ ] `NoTranslate` 列處理正確

更新 `TRANSLATION_LOG.md`：

1. 經本 skill 完成翻譯的檔案 → 列入「**完成翻譯**」分類（含完成日期）
2. 若該檔原在「尚未翻譯」或「已有翻譯尚未檢查」→ 自原分類移除
3. 在「變更紀錄」新增一列：日期、動作（完成翻譯）、檔案路徑與列數
4. 更新「盤查摘要」各分類數量

**分類定義：**

| 分類 | 條件 |
|------|------|
| 完成翻譯 | 經本流程確認並寫入 `tchinese` 譯文 |
| 已有翻譯尚未檢查 | 標題列有 `tchinese`，但非經本流程完成，或尚待品質檢查 |
| 尚未翻譯 | 標題列無 `tchinese` |

---

## 範例

### 精簡格式（新增欄位）

**翻譯前** `Key,english`：

```
Key,english
LockLoadingHack,Lock Loading Hack
```

**翻譯後** `Key,english,tchinese`：

```
Key,english,tchinese
LockLoadingHack,Lock Loading Hack,鎖具載入用
```

### 完整 TFP 格式（插入欄位）

標題：

```
Key,File,Type,UsedInMainMenu,NoTranslate,english,...,schinese,spanish
```

插入後：

```
Key,File,Type,UsedInMainMenu,NoTranslate,english,...,schinese,tchinese,spanish
```

資料列在 `schinese` 與 `spanish` 對應位置之間填入繁中譯文。

### 含格式標記

英文：

```
"DangerGirls Light Armors [e38d23](MS)"
```

繁中：

```
"DangerGirls 輕型護甲 [e38d23](MS)"
```

---

## 禁止事項

- **在步驟 2 確認表輸出後的同一輪對話中執行步驟 3～5**（最嚴重違規）
- 未見使用者對確認表的明確允許即修改檔案或更新 log
- 將「指定翻譯範圍」或「請開始翻譯」視同已確認列數並獲允許
- 改動 `Key`、英文欄或其他語系欄內容
- 刪除或重排既有欄位
- 將簡體 `schinese` 直接貼為 `tchinese`
- 批次處理時跳過確認表或跳過等待使用者回覆
