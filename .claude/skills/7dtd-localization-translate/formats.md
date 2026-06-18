# Localization.txt 格式參考

本專案常見格式彙整，供翻譯時對照。欄位名稱比對時**不分大小寫**。

## 格式 A：精簡雙欄

```
Key,english,tchinese
```

或尚未翻譯：

```
Key,english
```

範例模組：`Locks`、`PathSmoothing`、`7D1.0_Izayo_Visible_FPV_Gloves_mod`

## 格式 B：完整 TFP 多語

```
Key,File,Type,UsedInMainMenu,NoTranslate,english,Context / Alternate Text,german,latam,french,italian,japanese,koreana,polish,brazilian,russian,turkish,schinese,tchinese,spanish
```

- `tchinese` 標準位置：`schinese` 之後、`spanish` 之前
- 尚未翻譯時通常僅到 `english`，或有多語但無 `tchinese`

範例模組：`0-Quartz`、`7D1.0_Izayo_WeaponpackRemastered_*`、`zzz_REBIRTH__Utils`

## 格式 C：僅 english metadata

```
Key,File,Type,UsedInMainMenu,NoTranslate,english
```

範例模組：`zzzz_LittleRedSonja_Mumpfy_ArmorPack01_DangerGirls`

**處理**：在 `english` 欄之後追加 `,tchinese`；每列在英文欄位之後補譯文。

## 格式 D：自訂多語（無 tchinese）

```
Key,English,Spanish,German,Japanese,schinese,Russian
```

範例模組：`DroneLockToPlayer`

**處理**：在末尾加 `,tchinese`。

## 格式 E：其他精簡

```
Key,english,french,russian
```

範例模組：`TheDescent`

**處理**：在末尾加 `,tchinese`。

```
Key,Source,Context,english
```

或

```
Key,Source,Context,Changes,English,French,...
```

**處理**：辨識英文欄後，在末尾加 `,tchinese`。

## 欄位解析提示

| 欄位 | 說明 |
|------|------|
| `NoTranslate` = `x` | 該列通常不需翻譯，`tchinese` 留空 |
| `UsedInMainMenu` = `x` | UI 選單用文字，譯文宜簡短 |
| 引號欄位 | 內容可含逗號，整段須維持 `"..."` |
| 空列 | 保留，不刪除 |

## 英文欄位內常見需保留片段

- `[e38d23]` — 顏色代碼
- `(MS)` — 模組標記
- `{0}`、`{1}` — 遊戲內插值
- `[-]` — 換行或格式控制
