# SeaTunnel Docker Compose 設定指南

本說明檔提供使用 Docker Compose 部署 Apache SeaTunnel 時的基本設定流程，特別針對 Volume 掛載前的準備、設定檔導出與修改 Hazelcast 集群設定。

---

## 📦 步驟 1：啟動容器（不掛載 Volume）

為了取得預設設定檔，**首次執行 Docker Compose 時不需掛載 volume**，直接使用以下指令啟動容器：

```bash
docker-compose up -d
```

## 📁 步驟 2：導出設定檔
容器啟動後，從容器中將設定檔複製出來至本地資料夾（假設容器名稱為 temp_seatunnel）：
```bash
docker cp temp_seatunnel:/opt/seatunnel/config ./config
```
這會將 SeaTunnel 預設的設定檔複製到本機當前目錄下的 config 資料夾。

## 🛠️ 步驟 3：修改 Hazelcast 設定

編輯 config/hazelcast-master.yaml 檔案中的 join 區塊，設定正確的 IP 與 Port。如果執行環境為 localhost，請修改為以下內容：
```bash
join:
  multicast:
    enabled: false
  tcp-ip:
    enabled: true
    member-list:
      - seatunnel-master:5801
```

## ✅ 建議後續步驟

- 修改完 config 後，可將其透過 Volume 掛載至容器中以覆蓋預設設定。
- 更新 docker-compose.yml，加入 Volume 掛載路徑，例如：
```yml
volumes:
  - ./config:/opt/seatunnel/config

    # 重新啟動容器：
```
```bash
docker-compose down
docker-compose up -d
```


## 執行job.conf 執行數據ETL
- run seatunnel-client container