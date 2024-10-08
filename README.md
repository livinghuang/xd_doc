# XDA002 - LoRaWAN RMT 資料收集器

 XDA002專為從 RMT 資料收集讀數並通過 LoRaWAN 網路使用 ABP 模式傳輸而設計。

## 項目概述

XDA002 設備通過 RS485 通訊介面與 RMT 水表連接，並將液體流量數據發送到 LoRaWAN 網路進行遠程監控。

---

## 數據結構

液體流量計的數據以結構化的字節數組形式傳輸。

| **欄位**                   | **資料型別**  | **大小（字節）** | **描述**                   | **索引**      |
|----------------------------|---------------|------------------|-----------------------------|--------------|
| `main_battery_level`        | `uint8_t`     | 1                | 主電池電量                  | 0            |
| `reserve`                   | `uint8_t[3]`  | 3                | 保留位                       | 1 - 3        |
| `totalAccumulationValue`    | `byte[8]`     | 8                | 總流量累積值                 | 4 - 11       |
| `powerInsufficiencyDays`    | `uint8_t[2]`  | 2                | 電力不足的天數               | 12 - 13      |
| `deviceName`                | `byte[6]`     | 6                | 設備名稱                     | 14 - 19      |

### 字節數組範例

```plaintext
6e ff ff ff 00 00 00 31 96 02 00 05 00 00 00 52 35 10 20 11 47
```

### 解析字節數組

1. **主電池電量**：
   - **索引 0**：`6e`（十六進位）= 110（十進位）→ 電池電量為 110/255 ≈ 43% 或 6.43V。

2. **保留位**：
   - **索引 1-3**：`ff ff ff` → 保留值：`[255, 255, 255]`。

3. **總流量累積值**：
   - **索引 4-11**：`00 00 00 31 96 02 00 05` → 流量累積值：`3.19602 m³`。

4. **電力不足的天數**：
   - **索引 12-13**：`00 00` → 沒有檢測到電力不足。

5. **設備名稱**：
   - **索引 14-19**：`52 35 10 20 11 47` → 設備名稱：`"523510201147"`。

---

## 設置說明

### 電源連接

- **電源輸入範圍**：6-7V，通過 XH2.54-2 插座供電。
- **RMT 連接**：使用 XH2.54-4 為 RMT 供電並建立通訊。

### 讀取設備資訊

通過 USB 連接 XDA002 並打開串口控制台，系統啟動時會顯示如下信息：

```plaintext
Hello XDA002
ESP32ChipID=B0DF9B27843C
devEUI:3C84279BDFB00000
devAddr:88350FEA
nwkSKey:30303030623064663962323738343363
appSKey:30303030623064663962323738343363
BLE-1295323
```

---

## BLE 配置

XDA002 提供了一個 10 秒的 BLE 設置窗口，允許用戶進行配置，例如設置數據上傳間隔。

### 可用的 BLE 指令

1. **`AT`**：測試指令。
   - **回應**：`AT OK`

2. **`AT+UPINTV`**：設置或查詢數據上傳間隔（最大間隔：7 天或 604,800,000 毫秒）。
   - **查詢間隔**：
     - 指令：`AT+UPINTV=?`
     - 回應：`60000 ,OK`（當前間隔：60 秒）
   - **設置間隔**：
     - 指令：`AT+UPINTV=360000`
     - 回應：`360000 ,OK`（新間隔：1 小時）

### 範例：設置上傳間隔

使用 BLE 應用（如 nRF Connect）設置上傳間隔。

#### 操作步驟：
1. 打開 nRF Connect 應用。
      
   ![app](./xda002_img/app.png?raw=true)
2. 掃描名稱以 "XDA-" 開頭的設備。
      
   ![Scan](./xda002_img/filter.png?raw=true)
3. 連接目標設備。
      
   ![Connect](./xda002_img/devices.png?raw=true)
4. 訪問設備服務。
   
   ![Access](./xda002_img/services.png?raw=true)
5. 使用 AT 指令設置上傳間隔。
      
   ![Commands](./xda002_img/command.png?raw=true)
6.	確認回應。

   ![Verify](./xda002_img/result.png?raw=true)
