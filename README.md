# Wafer Position Match Tool

一套純前端（單一 HTML 檔案，免安裝）的半導體晶圓位置比對工具，專為 **FI（Front-end Interface）設備**的機械臂路徑模擬與 Wafer Map 角度對齊而設計。工程師可將 Wafer Map 截圖貼入工具，立即看到晶圓在每個傳送站點的旋轉角度，快速比對 Notch 方向是否符合製程規格。

---

## 專案介紹

在半導體製程中，晶圓（Wafer）從 Cassette（片盒）取出後，需依序經過 Load Lock（LL）、MetroFrame（MF）、Chamber 等多個站點。每個站點的機械臂（Robot Arm）會以特定角度夾持晶圓，導致 Wafer Map 上的 Notch（缺口）朝向不同。

本工具的核心功能：

1. **可視化機械臂路徑**：以拓樸圖形式呈現 FI 設備的完整模組佈局（CHA/CHB/CHC、MF、LL、Cooling、Cassette），並標出指定路徑上的各個晶圓站點。
2. **計算並顯示旋轉角度**：根據設備型號（FI5.X / FI6.4）、Cassette 位置（A/B/C/D）、手臂側（S1/S2）、目標站點，自動計算每個傳送點的 Robot 旋轉角度，並以紅色三角 Notch 指示器標示方向。
3. **Wafer Map 疊合顯示**：可上傳或貼上 Wafer Map 圖片，工具會自動將其疊合於各節點圓圈上，讓工程師一眼比對實際缺陷分佈與機械接觸位置的關聯。
4. **放大檢視**：點擊任意晶圓節點，彈出放大視窗，顯示該站點的 Slit Valve 方位與精確旋轉角度。

---

## 開發進程（版本歷史）

### 參考圖表

| 檔案 | 用途 |
|------|------|
| `FI.html` | FI Robot TYPE 2 接觸點位置圖（SVG 靜態參考圖），顯示 89mm / 96mm 接觸線、接觸墊（Pad）位置與 Sensor 位置 |
| `LL.html` | Load Lock 手臂接觸點幾何參考（SVG 草圖） |

---

### V1 — 基礎版本（`wafer position.html`）

**內部標題：Wafer Match Tool v3 - Slit Valve 幾何與佈局修正版 (含復原與清除)**

建立了工具的完整基礎架構：

- Canvas 尺寸：600 × 750 px（固定大小）
- 模組佈局：CHA、CHB、CHC、MF、LL、Cooling、Cass A/B/C/D
- 晶圓節點：A1/A2、B1/B2、C1/C2、MF1/MF2、LL1/LL2、CS、CA/CB/CC/CD
- 支援 **FI5.X / FI6.4** 兩種機台型號的角度查找表
- 支援 Cassette **A/B/C/D**、手臂側 **S1/S2**、目標站點 **LL/CHA/CHB/CHC**
- Offset（角度偏移量）輸入欄
- **雙 MAP 管理**（S1/S2 分別上傳不同 Wafer Map）
  - 上傳（Upload）、貼上（Paste / Ctrl+V）、清除（Clear）
  - 圓形縮圖預覽
- Wafer Map 疊合顯示（圓形裁切 + 旋轉繪製），裁切比例 0.94
- **紅色三角 Notch 指示器**
- **放大視窗**（點擊節點開啟 Modal）：500×500 px，顯示 Slit Valve 標示條與 Robot Angle
  - CHA 視角補正 +180°、CHB 補正 −90°、CHC 補正 +90°
- **歷史復原系統**（Undo，最多 20 步）
- **清除全部貼圖** 功能

---

### V2 — 佈局修正版（`wafer position_V2.html`）

**內部標題：同 V1**

修正 V1 的佈局問題：

- Canvas 高度擴大為 **850 px**（上下各增加 50px 留白）
- 所有 Y 座標整體向下平移 **+50px**，防止頂部節點被截切
- Wafer Map 裁切比例由 0.94 調整為 **1.01**（稍微放大，使圓形更填滿）

---

### V3 — FI Robot Contact Map 整合版（`wafer position_V3.html`）

**內部標題：Wafer Match Tool v4 - 整合 FI Robot Contact Map**

在 V2 基礎上新增 FI Robot 接觸分析功能：

- 新增 **FI_L / FI_R 兩個 FI Robot 晶圓節點**，顯示於 Cooling 模組兩側
  - FI_L → S1（左手臂），FI_R → S2（右手臂）
  - 在任何路徑下始終顯示，方便比對
- 新增 **FI Contact Overlay**（接觸點疊合標示）：
  - 中心垂直線（藍色）
  - ±89mm 藍色虛線（左右各一）
  - ±96mm 紅色實線（左右各一）
  - 上方接觸墊（Pad）：夾在 89mm 與 96mm 之間，左右各一（共 2 顆）
  - 下方 Sensor 小圓圈
  - 下方外部兩顆小 Pad（80°/100° 位置）
  - 89mm / 96mm 數值標註文字
  - **固定方位（不隨 Offset 旋轉），Notch 仍隨 Offset 旋轉**
- 放大視窗新增：
  - FI 節點放大時疊入**等比放大的 Contact Overlay**
  - 一般節點（LL1/LL2）增加 5 個接觸點圓圈標示
- 將 `drawNotch()` 抽出為獨立函式，提升代碼可讀性

---

### 英文版（`wafer position match tool -En.html`）

**標題：Wafer Position Match Tool-En**

V1 的英文使用者介面版本：

- 工具列全部改為英文（Target、Dual MAP Management、Undo、Clear All Images 等）
- 管理視窗新增操作提示文字（"Tip: You can use the Paste button or press Ctrl+V to paste an image."）
- 放大視窗新增 **站點標記圓點**（1/2/3/4，位於 1.5h/4.5h/7.5h/10.5h 時鐘位置），適用於 CHA/CHB/CHC 節點
- CHB 視角補正改為 +270°（與 V1 的 −90° 效果等效但寫法不同）
- Clipboard API 失敗時靜默記錄（不跳 alert）
- Wafer Map 裁切比例 0.92

---

### V4.1 — 響應式自動裁切版（`index.html`）

**標題：Wafer Match Tool v4.1 (Readable Labels)**

架構重寫的進化版本，強調實用性：

- **響應式 Canvas**：自動縮放至視窗大小（fitScale 演算法），支援視窗 resize
- **自動裁切演算法（Auto-Crop）**：自動掃描圖片像素，找出圓形 Wafer 的實際邊界，忽略截圖中的白色留白或標題列，確保圓形精確居中
- 簡化為**單一 Wafer Map 輸入**（移除 S1/S2 雙 MAP 管理）
- 新增清晰的站點標記（使用較大字體，依縮放比例動態調整）
- Popup 視窗顯示視角補正角度資訊（"View: -90°"）
- Slit Valve 標示列位置統一調整至 12 點鐘

---

### 測試版（`test.html`）

**標題：Wafer Position Match Tool-En（測試用）**

針對 **CHB/CHC 物理校正**的驗證版本：

- 移除 FI5.X/FI6.4 模式選擇（鎖定 FI5.X 基礎規則）
- 僅保留 **CHB、CHC** 站點選項（移除 CHA）
- 新增 **CHB 實機物理校正邏輯**：
  - S1 在 CHB 需指向 270°（Notch 背對 Slit Valve）
  - S2 在 CHB 需指向 90°（Notch 面對 Slit Valve）
  - 以 CHB 作為錨點角度，相對計算路徑上其他站點的角度

---

## 功能說明

### 工具列控制項

| 控制項 | 說明 |
|--------|------|
| **Mode（FI5.X / FI6.4）** | 選擇設備版本，影響各站點的旋轉角度查找表 |
| **CASS（A/B/C/D）** | Cassette 位置。A/B 為前兩組，C/D 為後兩組，影響是否需要經過 CS（Cross-Slot）站點 |
| **SIDE（S1/S2）** | 機械臂手臂側。S1 為左手臂（LL1 路徑），S2 為右手臂（LL2 路徑） |
| **目標 / Target** | 晶圓最終目的地（LL、CHA、CHB、CHC） |
| **Offset** | 角度偏移量（度），疊加至所有站點的旋轉角度，用於模擬預旋轉（Pre-align）後的狀態 |
| **雙 MAP 管理** | 開啟 S1/S2 分別管理 Wafer Map 的彈窗 |
| **復原 / Undo** | 回復上一步的 Wafer Map 狀態（最多 20 步） |
| **清除全部貼圖** | 移除所有已載入的 Wafer Map |

### 主畫面

- **藍色方框**：各功能模組（CHA/CHB/CHC、MF、LL、Cooling、Cassette A~D）
- **白色圓圈**：位於所選路徑上的晶圓站點（高亮顯示）
- **灰色圓圈**：不在當前路徑上的晶圓站點
- **紅色三角形**：Notch 方向指示器（三角尖端指向 Notch 位置）
- **紅色數字°**：該站點的 Robot 旋轉角度
- **紅色外框圓**：強調標示路徑上的站點

### 雙 MAP 管理視窗

- **S1（Left Arm）/ S2（Right Arm）**：分別管理兩支手臂的 Wafer Map
- **上傳**：從本機選擇圖片檔案
- **貼上**：從剪貼簿讀取圖片（或直接 Ctrl+V）
- **清除**：移除該側的 Wafer Map

### 放大視窗（點擊任意晶圓節點）

- 以 500×500 px 的大圓顯示該節點的 Wafer Map
- **Slit Valve 標示條**（藍色）固定於 12 點鐘位置，顯示站點名稱與旋轉角度
- 顯示 Robot Angle（度數）
- V3 中的 FI 節點放大時會額外顯示完整的接觸點幾何覆層

### 路徑計算邏輯

| Cassette | 手臂 | 路徑 |
|----------|------|------|
| A 或 B | S1 | Cass → LL1 → [MF1 → 目標] |
| A 或 B | S2 | Cass → CS → LL2 → [MF2 → 目標] |
| C 或 D | S1 | Cass → CS → LL1 → [MF1 → 目標] |
| C 或 D | S2 | Cass → LL2 → [MF2 → 目標] |

---

## 使用方式

### 快速開始

1. 使用任意現代瀏覽器（Chrome、Edge 建議）開啟 HTML 檔案，**無需安裝任何環境**。
2. 在工具列選擇：設備型號、Cassette 位置、手臂側、目標站點。
3. 主畫面自動更新，顯示路徑上的各站點角度。

### 載入 Wafer Map

**方式一：貼上（推薦）**
1. 在 Wafer Map 系統上截圖或複製圖片（Ctrl+C）。
2. 在工具上按 **Ctrl+V**，圖片會自動載入至目前選取的 SIDE（S1 或 S2）。

**方式二：雙 MAP 管理視窗**
1. 點擊工具列「**雙 MAP 管理**」按鈕。
2. 在 S1 或 S2 欄位點擊「**上傳**」選擇圖片，或點擊「**貼上**」從剪貼簿讀取。

**方式三：直接拖拉（index.html）**
- index.html 版本支援直接從資料夾拖拉圖片至上傳按鈕。

### 比對 Notch 方向

1. 載入 Wafer Map 後，各站點圓圈會顯示對應旋轉後的 Map 影像。
2. 紅色三角 Notch 指示器顯示晶圓缺口朝向。
3. 點擊節點可放大查看，確認 Notch 是否朝向（或背對）Slit Valve。
4. 調整 **Offset** 值可模擬 Pre-align 偏轉後的效果。

---

## 檔案結構

```
wafer position/
│
├── wafer position.html              # V1 基礎版（繁中 UI，FI5.X/FI6.4，雙 MAP）
├── wafer position_V2.html           # V2 佈局修正版（+50px 上下留白，裁切比例微調）
├── wafer position_V3.html           # V3 FI Contact Map 整合版（FI_L/FI_R 節點、接觸點幾何）
│
├── wafer position match tool -En.html  # 英文版（V1 架構，鐘點標記，靜默 Clipboard 錯誤）
│
├── index.html                       # V4.1 響應式版（Auto-Crop、視窗縮放、單 MAP 輸入）
│
├── test.html                        # 測試版（CHB/CHC 物理校正驗證，移除 FI5.X/FI6.4 選項）
│
├── FI.html                          # FI Robot TYPE 2 接觸點位置參考圖（靜態 SVG）
├── LL.html                          # Load Lock 手臂接觸點幾何草圖（SVG）
│
└── README.md                        # 本說明文件
```

---

## 技術實作細節

### 核心技術

- **純 HTML5 + Vanilla JavaScript**，無框架依賴，單一檔案即可執行
- **HTML5 Canvas API**：所有繪圖（模組方框、晶圓節點、Notch 指示器、Wafer Map 疊合）均以 Canvas 2D Context 繪製
- **Clipboard API + FileReader API**：支援剪貼簿圖片讀取與本機檔案上傳

### 角度計算邏輯

每個站點的 Robot 旋轉角度由以下參數決定：

```
angle = (查找表基礎角度 + Offset) % 360
```

查找表依 `(Mode, Cassette, Side)` 三個維度索引，返回各站點（LL1/LL2/A1/A2/B1/B2/C1/C2/CS）的基礎角度。

畫面顯示角度：
```
displayAngle = (180 + angle) % 360
```
此轉換使 angle=0° 時 Notch 指向 6 點鐘（Canvas 座標系中向下為 90°）。

### Wafer Map 繪製

使用 Canvas `clip()` 方法將圖片裁切為圓形，再以旋轉 Matrix 繪製：

```javascript
ctx.save();
ctx.beginPath();
ctx.arc(cx, cy, r, 0, Math.PI * 2);
ctx.clip();
ctx.translate(cx, cy);
ctx.rotate(displayAngle * Math.PI / 180);
ctx.drawImage(img, srcX, srcY, cropSize, cropSize, -r, -r, r*2, r*2);
ctx.restore();
```

### Auto-Crop 演算法（index.html）

從圖片中心向四個方向掃描像素，找到第一個連續白色區域（15 px 寬的空白帶）作為邊界，自動計算正方形裁切框，精確框住圓形 Wafer 區域，忽略截圖邊框或標題文字。

---

## 設備術語對照

| 術語 | 說明 |
|------|------|
| **FI** | Front-end Interface，半導體設備前端介面模組 |
| **CASS / Cassette** | 晶圓片盒（存放 25 片晶圓） |
| **LL / Load Lock** | 裝載站，大氣與真空腔體的緩衝區 |
| **MF / MetroFrame** | 中間框架，用於晶圓對準量測 |
| **CHA/CHB/CHC** | Process Chamber，製程腔體 |
| **CS / Cross-Slot** | 交叉槽，用於在 S1 與 S2 路徑之間切換 |
| **S1 / S2** | 機械臂的兩支手臂（S1=左臂，S2=右臂） |
| **Notch** | 晶圓邊緣的缺口，用於對準方向基準 |
| **Slit Valve** | 腔體閥門，晶圓進出腔體時經過的開口 |
| **Robot Angle** | 機械臂夾持晶圓時的旋轉角度 |
| **89mm / 96mm** | FI Robot 接觸點到晶圓中心的距離（用於判斷接觸區域） |
