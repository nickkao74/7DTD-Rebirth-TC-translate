---
name: 7dtd-localization-translate
description: >-
  Translates 7 Days to Die mod Localization.txt files to Traditional Chinese
  (tchinese). Use when the user asks to translate Localization.txt,
  localization.txt, add tchinese column, or work on Rebirth mod TC translation
  for a specified file or scope.
---

# 7DTD Rebirth Localization 繁中翻譯

將模組 `Config/Localization.txt`（或 `localization.txt`）的英文欄位翻譯為繁體中文，並寫入 `tchinese` 欄位。

## 觸發與範圍

依使用者指定處理：

| 指定方式 | 行為 |
|----------|------|
| 單一檔案路徑 | 只處理該檔 |
| 模組資料夾名稱 | 處理 `{資料夾}/Config/Localization.txt` |
| 「尚未翻譯」清單 | 讀取 `TRANSLATION_LOG.md` 中對應項目 |
| 多檔 / 批次 | 逐檔走完完整流程，**每檔皆須使用者確認後才寫入** |

檔名大小寫不拘（`Localization.txt` / `localization.txt`）。

---

## 工作流程（必須依序執行）

```
進度：
- [ ] 1. 解析範圍並讀取檔案
- [ ] 2. 分析格式與列數 → 向使用者確認（等待允許）
- [ ] 3. 新增或補齊 tchinese 欄位
- [ ] 4. 翻譯並填入
- [ ] 5. 驗證並更新 TRANSLATION_LOG.md
```

### 步驟 1：讀取檔案

1. 讀取目標 `Localization.txt` 全文。
2. 記錄：相對路徑、標題列、資料列數、是否已有 `tchinese` 欄位。
3. **列數定義**：
   - **總列數**：檔案中非空行數（含標題列）
   - **待翻譯列數**：需填入 `tchinese` 的資料列（排除標題；`NoTranslate` 為 `x` 的列可標記為跳過）

### 步驟 2：向使用者確認（必須等待允許）

**在取得使用者明確同意前，不得修改檔案。**

回報格式：

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

請確認是否開始翻譯。回覆「可以」、「開始」或類似允許後再繼續。
```

使用者未明確允許時，僅能分析與預覽，**不可寫檔**。

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

1. 若該檔原在「尚未翻譯」→ 移至「已有翻譯尚未檢查」
2. 在「變更紀錄」新增一列：日期、動作（完成翻譯）、檔案路徑
3. 更新「盤查摘要」數量（若使用者要求同步）

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

- 未經步驟 2 使用者允許即修改檔案
- 改動 `Key`、英文欄或其他語系欄內容
- 刪除或重排既有欄位
- 將簡體 `schinese` 直接貼為 `tchinese`
- 批次處理時跳過逐檔確認（除非使用者一次明確授權整批）
