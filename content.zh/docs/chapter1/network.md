---
weight: 3
title: "網路設定"
---

# Docker 介紹與 Docker 環境建置

如前所述(執行環境安裝的 step2)，進入 dockerd-src 目錄，執行`source create_docker_env.sh`, 就可以安裝 docker 並建立 docker network，安裝的過程中，需輸入主機使用者密碼，此使用者須有 sudo 權限，才可以正確安裝。正確安裝後，可以顯示 docker 版本資訊，如圖 5 所示

![圖5: docker版本](../../../assets/fig5.png)

若 create_docker_env.sh 正常完成，停留在圖 6 的畫面，按下Ctrl+c 即可跳出。

![圖6: 正確的docker狀態](../../../assets/fig6.png)
圖6: 正確的docker狀態

# PEDSA 啟動注意事項及各服務說明

啟動系統前，需先確認表 6 所列的通訊埠(port)是否開通:

表5: 服務端需要開通的通訊埠

| 通訊埠號 | 說明                     |
| -------- | ------------------------ |
| 22       | 服務端與使用端登入連線用 |
| 11016    | 服務端 API 通訊埠        |
| 443      | 服務端 SSL 通訊埠        |
| 11100    | 資料庫                   |
| 6922     | 服務端 Hadoop            |
| 5922     | K 匿名與使用端 Hadoop    |
| 5088     | 合成 API                 |
| 5915     | K匿名與使用端API         |
| 5090     | 差分API                  |

如前所述(執行環境安裝的 step4)，經由 step4 步驟啟動後，會產生 PETWEB 、 PETservice 、 PET_join 、 PETSHadoop 、PETSWebservice、pets_dp 及 pets_syn 等 7 個 docker 服務叢集，可執行`docker stack ls` 得到相關資訊。服務端系統的服務如下表內容:

表6: PEDSA 服務端系統說明

| PEDSA 系統服務             | 說明                                                                  |
| -------------------------- | --------------------------------------------------------------------- |
| pets_web、pets_service     | PEDSA 系統的管理介面，管控 K 匿名、資料生成及差分隱私三個隱私強化技術 |
| PET_join_Hadoop            | 負責整合各單位資料的安全鏈結                                          |
| PETSHadoop、PETSWebservice | K 匿名服務                                                            |
| pets_syn                   | 資料合成服務                                                          |
| pets_dp                    | 差分隱私服務                                                          |

服務端使用兩個 docker 網路，分別為:

- hadoopnet_overlay: 使用的服務有 pets_syn、pets_dp、pets_web、pets_k 與 pets_service。
- hadoopnet_pet_overlay: 由 pets_hadoop 所使用。

正確的 PEDSA 啟動，可以由執行:

```sh
docker service logs pets_service_fastapi
```

正確的啟動畫面如下圖:
![圖8: 正確的 pets_service 啟動畫面](../../../assets/fig8.png)

圖8: 正確的 pets_service 啟動畫面

當服務端正確的啟動，就可以在瀏覽器連接:

- https://IP_addr
- https://IP_addr/kweb
- https://IP_addr/synweb
- https://IP_addr/dpweb

等4個網頁, 若是能正確開啟網頁即是正確啟動。

- PEDSA服務端目錄結構:

![圖9: PETS的目錄結構](../../../assets/fig9.png)

- docker 服務檢視:

```sh
docker service ls
```

![圖10: 組成PEDSA系統之容器](../../../assets/fig10.png)

# PEDSA安裝流程介紹 - 使用端

為了正確啟動使用端，開放的通訊埠如下:

表7: 服務端需要開通的通訊埠

| 通訊埠號 | 說明                           |
| -------- | ------------------------------ |
| 22       | 服務端與使用端登入連線用       |
| 443      | 使用端 SSL通訊埠               |
| 5915     | 加密欄位及安全資料鏈結用通訊埠 |
| 11050    | 使用端用通訊埠                 |

使用端使用 hadoopnet_web_overlay 網路連結服務容器，由於與服務端使用不同 docker 網路，因此同一主機可以同時啟動服務端和使用端。

當使用端正確的啟動，就可以在瀏覽器連接:

- https://IP_addr:11050

# 系統重設與重新安裝(包含移機，重新安裝)

在移機或重新安裝系統時，需要先停止現有的 docker 服務，以避免程式因程式仍在執行而無法複製。可執行

```sh
sudo systemctl stop docker
```

停止 docker 服務。如果 docker 服務被
docker.socket 喚醒，則可能需要額外執行 `systemctl stop
docker.socket` 以確保服務完全停止。其他就如同安裝步驟。
