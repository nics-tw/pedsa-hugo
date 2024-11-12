# 隱私共享優化佈署方案(GAU)

1. 部署步驟優化(分使用端與服務端)
   解壓縮PEDSA系統程式及相關工具軟體:

- `deploy_PEDSA_1012.tar.gz`包含PEDSA系統所需的程式及第三方工具軟體，首先執行`tar -xzvf deploy_PEDSA_1012.tar.gz`，解壓縮後會產生目錄deploy_PEDSA，目錄的內容如下：
  - deploy_pedsa.sh: 部署PEDSA的安裝程式，即是解壓縮下面的\*.tar.gz，並修正目錄權限。
  - pedsa_s.tar.gz: PEDSA服務端的程式及設定檔等 ，解開的目錄名稱為pedsa_s
  - pedsa_c.tar.gz: PEDSA使用端的程式及設定檔，解開的目錄名稱為pets_dir_v1
  - pedsa_images.tar.gz: PEDSA系統所需映像檔，解開的目錄名稱為pedsa_images
  - dockerd-src-v26-withTLSCert1012.tar.gz: docker離線安裝程式 ，解開的目錄名稱為dockerd-src

2. PEDSA服務端系統安裝步驟：
   a. `source deploy_PEDSA/deploy_pedsa.sh`，輸入指定安裝路徑($DEPLOY_DIR)。
      i.  `$DEPLOY_DIR`階層結構：
      - dockerd-src
      - pedsa_images
      - pedsa_s
      - pets_dir_v1
b.`source $DEPLOY_DIR/dockerd-src/create_docker_env.sh`，輸入
      a. Host IP: docker host ip address # 啊為啥不直接寫個script抓本機IP就好？？？甚至根本不用手動輸入啊？？？
      b. PEDSA安裝目錄：即`$DEPLOY_DIR/`
   c. Host User PSWD: # 如果user不是sudoer確定可以？？？
   d. pass phrase for ./ca-key.pem: iclw200@ ＃這到底是什麼鬼？？？

NOTE: 指令執行完畢會建立docker環境，docker network，及mount下載目錄。安裝過程有幾個可能發生狀況，若是主機是第一次安裝docker環境，並在執行docker的基本指令，如docker ps，出現權限不足的錯誤時，需關閉目前的terminal，再開啟新的terminal再進行後續步驟。 # r u sure ???
NOTE:若是看到下載目錄不存在的提示，則需執行mount下載目錄的程式，這會在安裝服務端的步驟(step4)詳述。