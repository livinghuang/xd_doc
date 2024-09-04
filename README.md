Your document is already well-organized and clear. Here's a slightly refined version with minor tweaks for clarity and flow:

---

# XDA002 - LoRaWAN Liquid Flow Meter

Welcome to the XDA002 project, designed to collect readings from an RMT water meter and transmit them to a LoRaWAN network using ABP mode.

## Project Overview

The XDA002 device connects to an RMT water meter via RS485 communication and forwards liquid flow data to the LoRaWAN network for remote monitoring.

### Key Components
- **RS485 Communication**: Interfaces with the water meter through a MAX485 module.
- **LoRaWAN Transmission**: Utilizes the Heltec HT-CT62 module to send data over wide areas.

---

## Data Structure

The flow meter's data is sent as a structured byte array.

| **Field**                  | **Data Type** | **Size (Bytes)** | **Description**              | **Index**    |
|----------------------------|---------------|------------------|------------------------------|--------------|
| `main_battery_level`        | `uint8_t`     | 1                | Main battery level            | 0            |
| `reserve`                  | `uint8_t[3]`  | 3                | Reserved for future use       | 1 - 3        |
| `totalAccumulationValue`    | `byte[8]`     | 8                | Total flow accumulation       | 4 - 11       |
| `powerInsufficiencyDays`    | `uint8_t[2]`  | 2                | Days with insufficient power  | 12 - 13      |
| `deviceName`                | `byte[6]`     | 6                | Device identifier             | 14 - 19      |

### Example Byte Array

```plaintext
6e ff ff ff 00 00 00 31 96 02 00 05 00 00 00 52 35 10 20 11 47
```

### Parsed Byte Array

1. **Main Battery Level**:
   - **Index 0**: `6e` (hex) = 110 (decimal) → Battery level is 110/255 ≈ 43% or 6.43V.

2. **Reserve**:
   - **Index 1-3**: `ff ff ff` → Reserved: `[255, 255, 255]`.

3. **Total Accumulation Value**:
   - **Index 4-11**: `00 00 00 31 96 02 00 05` → Flow accumulation: `3.19602 m³`.

4. **Power Insufficiency Days**:
   - **Index 12-13**: `00 00` → No power insufficiency detected.

5. **Device Name**:
   - **Index 14-19**: `52 35 10 20 11 47` → Device name: `"523510201147"`.

---

## Setup Instructions

### Power Connection

- **Power Input Range**: 6-7V via the XH2.54-2 socket.
- **RMT Connection**: Use XH2.54-4 for power and communication with the RMT.

### Retrieving Device Information

Connect the XDA002 to a USB port and access the serial console to read device information during startup:

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

## BLE Configuration

You can configure the XDA002 via BLE within a 10-second window after powering on. One important setting is the data upload interval.

### Available BLE Commands

1. **`AT`**: Basic test command.
   - **Response**: `AT OK`

2. **`AT+UPINTV`**: Set or retrieve the data upload interval (max: 7 days or 604,800,000 ms).
   - **Get Interval**:
     - Command: `AT+UPINTV=?`
     - Response: `60000 ,OK` (current interval: 60 seconds)
   - **Set Interval**:
     - Command: `AT+UPINTV=360000`
     - Response: `360000 ,OK` (new interval: 1 hour)

### Example: Setting the Upload Interval

To set the upload interval, use a BLE app such as nRF Connect:

1. **Open** the nRF Connect app.
   
   ![app](./xda002_img/app.png?raw=true)
2. **Scan** for devices named "XDA-".
   
   ![Scan](./xda002_img/filter.png?raw=true)
3. **Connect** to the desired device.
   
   ![Connect](./xda002_img/devices.png?raw=true)
4. Access the **services** section.
   
   ![Access](./xda002_img/services.png?raw=true)
5. **Send AT commands** to configure the upload interval.
   
   ![Commands](./xda002_img/command.png?raw=true)
6. **Verify** the response.
   
   ![Verify](./xda002_img/result.png?raw=true)
---

## Conclusion

XDA002 simplifies the process of monitoring liquid flow by combining LoRaWAN connectivity with an RMT water meter. It offers flexible configuration through BLE and efficient long-range data transmission for remote monitoring needs.

---



---

# XDA002 - LoRaWAN 液體流量計

歡迎來到 XDA002 項目，該系統專為從 RMT 水表收集讀數並通過 LoRaWAN 網路使用 ABP 模式傳輸而設計。

## 項目概述

XDA002 設備通過 RS485 通訊介面與 RMT 水表連接，並將液體流量數據發送到 LoRaWAN 網路進行遠程監控。

### 主要元件
- **RS485 通訊**：通過 MAX485 模組與水表進行通訊。
- **LoRaWAN 傳輸**：使用 Heltec HT-CT62 模組進行廣域數據傳輸。

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
---

## 結論

XDA002 將 LoRaWAN 通訊與 RMT 水表整合，實現高效的液體流量數據監控。它提供靈活的 BLE 設置和高效的遠距數據傳輸，是遠程水表監控的理想工具。