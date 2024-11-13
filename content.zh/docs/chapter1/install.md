---
weight: 2
title: "安裝流程"
---

本系統手冊將描述如何在 Linux Ubuntu 系統上利用 docker 部署微服務架構，進而組成 PEDSA 系統。安裝的流程如圖 3:

![部署流程圖](以後補) TODO:

系統環境資源建議：此步驟會依據基準資料列出所需的主機的基本需求，導入機關再依據需求的資料量、分析案例規模彈性調整主機的硬體資源。

執行環境安裝:在主機上安裝 docker 執行環境，建置 docker 網路及安裝 PEDSA 應用程式。

PEDSA 啟動:會介紹如何啟動服務端及使用端系統，詳細的內容於"前置作業及軟硬體介紹"章節解說。

# 安裝前置作業及軟硬體介紹

## 系統環境資源建議

導入機關選擇硬體規格時，可參考表 1 的基準資料及表 2的硬體資訊:

表1: 基準資料特徵

| 資料筆數  | 預估大小 | 欄位 | 直接識別 | 間接識別 | 敏感欄位 |
| --------- | -------- | ---- | -------- | -------- | -------- |
| 1,000,000 | 1G       | 15   | 2        | 6        | 4        |

表2: 建議之硬體資源

| 最低需求 | CPU(core) | RAM(GB) | Storage(GB) |
| -------- | --------- | ------- | ----------- |
| K匿名    | 8         | 16      | 1024        |
| 合成資料 | 8         | 32      | 512         |
| 差分隱私 | 4         | 16      | 256         |

PEDSA 建議於 Linux Ubuntu 22.04 LTS 版本進行安裝。表 1 及表 2 的推論過程放在附錄的"環境評估資源"段落，方便導入機關做進一步探討。

## 執行環境安裝

### 部署步驟優化(分服務端與使用端)

考量部分導入機關需在隔離的環境中進行軟體安裝，提供shell script 和 tar 檔案，允許在沒有網際網路連接的情況下，經由 tar 檔案取得安裝所需的所有檔案，並透過 shell script 執行安裝指令，以確保在受限的環境中也能夠順利地部署系統。

#### 解壓縮 PEDSA 系統程式及相關工具軟體

`deploy_PEDSA_1012.tar.gz` 包含 PEDSA 系統所需的程式及第三方工具軟體，首先執行

```sh
source tar -xzvf deploy_PEDSA_1012.tar.gz
```

解壓縮後會產生目錄 deploy_PEDSA，目錄的內容如表 3:

表3: 部署檔案說明

| 檔案名稱                               | 說明                                                               |
| -------------------------------------- | ------------------------------------------------------------------ |
| deploy_pedsa.sh                        | 部署 PEDSA 的安裝程式，即是解壓縮下面的\*.tar.gz，並修正目錄權限。 |
| pedsa_s.tar.gz                         | PEDSA 服務端的程式及設定檔等，解開的目錄名稱為 pedsa_s             |
| pedsa_c.tar.gz                         | PEDSA 使用端的程式及設定檔，解開的目錄名稱為 pets_dir_v1           |
| pedsa_images.tar.gz                    | PEDSA 系統所需映像檔，解開的目錄名稱為 pedsa_images                |
| dockerd-src-v26-withTLSCert1012.tar.gz | docker 離線安裝程式 ，解開的目錄名稱為 dockerd-src                 |

#### PEDSA 服務端系統之安裝步驟

##### Step1

```sh
cd deploy_PEDSA
source ./deploy_pedsa.sh
```

會提示輸入安裝路徑 (\$deploy_dir) ，執行完畢會在\$deploy_dir 會產出 pedsa_s, pets_dir_v1 ,dockerd-src 及 pedsa_images 等目錄，如上表所示，假設$deploy_dir 為 /home/itri-pedsa/ deploy_PEDSA，則有圖 4 的檔案結構:

![圖4: deploy_PEDSA檔案結構]

##### Step2

```sh
cd dockerd-src
source ./create_docker_env.sh
```

安裝過程中需輸入以下參數：

- 主機 IP(例如 34.81.71.21): docker 宿主主機的 IP 地址
- 輸入 PEDSA 安裝目錄: 此安裝目錄就是 pedsa_s 的所在位置，以上圖為例 ， pedsa_s 的位置為 /home/itri-pedsa/deploy_PEDSA，則輸入 PEDSA 安裝目錄就為 /home/itri-pedsa/deploy_PEDSA
- Host User Password: 輸入主機使用者密碼，若是沒有使用者密碼則不需輸入
- Enter pass phrase for ./ca-key.pem: 輸入 iclw200\@

指令執行完畢會建立 docker 環境，docker network，及 mount 下載目錄。安裝過程有幾個可能發生狀況，

- 若是主機是第一次安裝 docker 環境，並在執行 docker 的基本指令，如 docker ps，出現權限不足的錯誤時，需關閉目前的 terminal，再開啟新的 terminal 再進行後續步驟。
- 若是看到下載目錄不存在的提示，則需執行 mount 下載目錄的程式，這會在安裝服務端的步驟(Step4)詳述。

##### Step3

```sh
cd pedsa_images
source ./load_docker_imgs.sh
```

安裝系統需要的映像檔

###### Step4

```sh
cd pedsa_s
source ./petsStartUp.sh
```

安裝服務端過程中需輸入以下參數：

- 輸入 PEDSA 安裝目錄: 此安裝目錄就是 pedsa_s 的所在位置，以上圖為例 ， pedsa_s 的位置為 /home/itri-pedsa/deploy_PEDSA，則輸入 PEDSA 安裝目錄就為 /home/itri-pedsa/deploy_PEDSA
- 主機 IP(例如 34.81.71.21): docker 宿主主機的 IP 地址
- Host User Password: 輸入主機使用者密碼，若是沒有使用者密碼則不需輸入

安裝後須注意下列狀況:

- 若是在 Step2 看到下載目錄不存在的提示或是無法下載檔案，則執行`source ./mountDownloadFilePATH.sh`，執行過程需輸入以下參數與後續步驟：
  - 輸入 PEDSA 安裝目錄: 輸入服務端 pedsa_s 的安裝位置
  - Host User Password: 輸入主機使用者密碼，若是沒有使用者密碼則不需輸入
  - 執行完畢需重新執行 `source ./petsStartUp.sh`。
- 若發現使用端無法上傳資料給服務端，則執行`source ./gen_ssh_key.sh`，執行過程有數個提示如下：
  - 輸入 PEDSA 使用端的安裝目錄: 當 pets_dir_v1 的路徑為 /home/itripedsa/deploy_PEDSA/pets_dir_v1 則安裝目錄為 /home/itri-pedsa/deploy_PEDSA
  - 輸入 PEDSA 使用端的使用者名稱: 安裝使用端主機的使用者名稱
  - 輸入 PEDSA 使用端的使用者密碼: 安裝使用端主機的使用者密碼
  - 輸入 PEDSA 使用端的 IP: 安裝使用端主機的 IP 地址
