# KanbanFlow 使用規範

## 新增看板（Board）

## 管理看板

## 新增 Swinlane

## 管理 Swinlane

## 新增卡片（Card）
### 命名規則
新增的卡片的名稱必須參照以下格式：

`[story-${storyNumber}]身為xxx，我想要xxx，如此一來，我可以xxx`

**特別注意：** `${storyNumber}` 不能與其他已存在的 `${storyNumber}` 重複。
在選定 `${storyNumber}` 時，必須找出目前存在最大的 `${maxStoryNumber}`，並加上 1。
```
${storyNumber} = ${maxStoryNumber} + 1
```

舉例而言：

`[story-2]身為 Narwhale 的使用者，我想要優化 Narwhale 的使用方式，好讓我能夠更輕易地找到我想要找的資料。`

### 標籤（Label）
每個在 Sprint 裡規劃要做的卡片都必須給予標籤，標籤可以多個，**但一定要標籤此卡片屬於哪個 Sprint，以及它的`${StoryNumber}`**。

假設新增的卡片，在 `sprint-18`，而卡片的 `${storyNumber}` 為 `2`，則必須標注以下標籤：
`sprint-18`, `story-2`

### 關聯（Relation）
每張卡片（除了 Root Story 本身）都必須增加 `Relation`，
1. 若是兩張卡片有**相關性** -> 在其中一張卡片增加 `Relates to`
2. 若是兩張卡片有**相依性** -> 在其中一張卡片增加 `Depends on` 或是 `Required by`。舉例而言， A 卡片需要 B 卡片做完才算完成，則 `A Depends on B` 
或是 `B Required by A`。

### 成員（Member）
**每張卡片只分配給一個成員處理**，若有多人要做同一個卡片，那每個都必須開一張屬於自己的卡片。

### 內容（Description）
每張卡片都必須詳述其內容，內容包含下列五大項。
```
# 問題描述
# 功能描述
# 驗收條件
# 更新進度
# 相關文件
```

舉例而言：
```
# 問題描述
目前有兩個書籤功能：
1. 書籤頁面(`/bookmarks`)
2. 浮在文章頁右側，提示讀者收藏書籤的按鈕

而這兩個功能，目前都是在 client side rendering 時產生，
導致畫面會有閃爍的問題。

# 功能描述
提供 server side rendering ，在組頁面時，就已經將書籤所需要的資料帶入，並且 render 出結果。

# 驗收條件
## Client side rendering
### 在讀者尚未登入的狀態下：
1. 若點選 header 上的書籤按鈕 -> 導讀者到登入頁面 -> 讀者登入後自動導回書籤頁面(`/bookmarks`)
2. 若點選文章夜右側浮動的書籤按鈕 -> 導讀者到登入頁面 -> 讀者登入後自動導回文章頁面

### 在讀者登入的狀態下：
1. 若點選 header 上的書籤按鈕 -> 導到 `/bookmarks` 頁面並且詳列讀者所收藏的文章
2. 若點選文章夜右側浮動的書籤按鈕 -> 從書籤列表裡新增／刪除該文章

## Server side rendering
### 在讀者尚未登入的狀態下:
1. 若使用者直接連到 `/bookmarks` 頁面 -> 導讀者到登入頁面 -> 讀者登入後導回 `/bookmarks`

2. 文章夜右側浮動的書籤按鈕呈現尚未收藏的狀態

### 在讀者登入的狀態下:
1. render 出讀者書籤收藏的第一分頁

2. 文章夜右側浮動的書籤按鈕呈現收藏的狀態

* 程式碼必須上正式環境

# 更新進度
# 相關文件

```
## 管理卡片
