# Anonymizer 去識別化工具

把文字或檔案中的路徑、使用者名稱、IP、Email、帳號密碼換成隨機代號，再貼給 AI。
單一 `index.html`，全部在瀏覽器本機處理，不會上傳任何資料，不需安裝、不需網路。

## 使用方式

1. 用瀏覽器開啟 `index.html`。
2. 在「① 原始文字」貼上內容，「② 去識別化結果」會即時產生，按「複製」後貼給 AI。
3. 取得 AI 回覆後，到「③ 還原 AI 回覆」貼上，即可把代號換回原始內容。

## 功能

- **遮蔽項目**（可個別開關）
  - Windows 路徑（`C:\...`、`C:/...`、`\\主機\分享`）
  - Linux / macOS 路徑（`/home/...`、`~/...`）
  - IPv4 / IPv6 / MAC 位址（可保留 127.0.0.1、::1 等本機位址）
  - Email
  - 帳號、密碼、Token（`password=...`、`Bearer ...`、`user:pass@host` 等）
  - 私鑰：`-----BEGIN ... PRIVATE KEY-----` 到 `-----END ... PRIVATE KEY-----` 整段換成一個代號（RSA、EC、OpenSSH、加密私鑰皆可）；缺少結尾行的截斷內容也會遮蔽後續的標頭與 Base64 行
  - 設備設定檔密碼（Cisco / Arista、Juniper、Fortinet）：只遮蔽「值」，保留關鍵字與引號
    - Cisco / Arista：`enable secret/password`、`username ... secret/password`、`snmp-server community`、`tacacs-server key`、`radius-server key`、server 區塊內的 `key 7 ...`、`ntp authentication-key`、`message-digest-key`、`crypto isakmp key`、`wpa-psk`、`ppp chap/pap password`、帶類型編號的 `password 7 ...`、`secret 5 ...`、`key-string 7 ...`，以及 `$1$` `$5$` `$6$` `$8$` `$9$` 等雜湊字串；Cisco 點分 MAC（`0011.2233.4455`）併入 IP / MAC 項目
    - Juniper：`encrypted-password`、`authentication-key`、`secret`、`ascii-text`、SNMP `community`（含 `authorization` 區塊）、`user NAME {`
    - Fortinet：`set ... ENC ...`、`set password/passwd/psksecret ...`
  - 其他位置出現的使用者名稱
  - 自訂關鍵字（逗號或換行分隔，例如公司名、主機名稱）
- **路徑取代方式**
  - 保留結構：逐層目錄各換成代號
  - 只保留檔名：前面的目錄整段取代
  - 整段路徑取代
- **隨機代號**：格式如 `[USER-6vvit]`、`[D-evmae]`；同一個原文永遠對應同一個代號。可關閉類型前綴，也可一鍵重新產生。
- **批次處理檔案**：拖曳或點選上傳多個檔案，可預覽、單檔下載、全部下載、合併下載、合併複製；可選編碼（UTF-8、Big5、UTF-16 LE、Shift_JIS），輸出一律為 UTF-8，下載檔名也可去識別化；二進位檔會自動略過。
- **對照表**：只列出目前「① 原始文字」與批次檔案用到的代號，隨輸入即時更新；可複製或下載 JSON，還原時可匯入。本機記住的代號不會顯示在表中，按「重新產生所有代號」才會清除。
- **設定與關鍵字區塊**：可摺疊（狀態會記住），標題旁顯示關鍵字數量。關鍵字可「打包下載」成 JSON、「上傳匯入」（JSON 或文字檔，與目前內容合併去重）、「存為預設」「載入預設」與「清空」；第一次開啟且尚無任何設定時，會自動套用預設關鍵字。
- **版面順序**：① ② 輸入與結果 → 批次處理檔案 → ③ 還原 AI 回覆 → 設定與關鍵字 → 對照表。

## 範例

```
原文：C:\Users\WangXiaoMing\Documents\ACME\project-x\src\config.json
結果：C:\Users\[USER-6vvit]\Documents\[D-evmae]\[D-5q6ef]\src\config.json
```

## 注意事項

- 對照表預設會存在瀏覽器的 localStorage，重新整理後仍可還原。其中包含原始路徑與帳密，不想留存請關閉「在本機瀏覽器記住對照表」，或在共用電腦上使用後清除瀏覽器資料。
- 規則式遮蔽無法保證涵蓋所有敏感資訊，貼給 AI 前請自行檢查結果。
- 設備設定檔目前尚未處理：`hostname`、`host-name`、網域名稱、`description`、`banner`、SNMP location / contact、Fortinet 的 SNMP community 名稱、AS 號碼、序號與授權。這些請用「額外關鍵字」自行加入。
- 設備密碼規則只收錄語法明確的寫法；明碼的 `password xxx`（例如 `line vty` 下未加密的密碼）、`snmp-server host` 的 community、`pre-shared-key`（Cisco IKEv2 / Juniper 之外的變形）等未處理，請自行檢查。
- 「目錄名稱可含空白」開啟時，若路徑中間夾雜一般句子，可能會多吃到文字，這時請關閉該選項。

## 檔案

- `index.html`：工具本體
- `README.md`：本說明
