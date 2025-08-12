# Zookeeper 客戶端服務 ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2024.0.2-blue.svg)](https://spring.io/projects/spring-cloud)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

這是一個基於 Spring Cloud 的微服務客戶端應用程式，使用 Apache Zookeeper 作為服務註冊與發現中心。專案展示了如何在微服務架構中實現服務間的動態發現和負載均衡。

### 核心功能
- **服務註冊**：自動向 Zookeeper 註冊服務資訊
- **服務發現**：動態發現其他微服務實例
- **負載均衡**：使用 Spring Cloud LoadBalancer 實現請求分發
- **HTTP 客戶端**：整合 Apache HttpClient 5 進行服務間通訊
- **健康檢查**：提供應用程式健康狀態監控

### 解決問題
- 解決微服務架構中服務發現的複雜性
- 提供高可用的服務註冊與發現機制
- 實現服務間的動態負載均衡
- 簡化微服務間的網路通訊

### 目標使用者
- 微服務架構開發者
- Spring Cloud 學習者
- 分散式系統工程師

> 💡 **為什麼選擇 Zookeeper？**
> - 提供強一致性保證（CP 特性）
> - 成熟的分散式協調服務
> - 豐富的監控和管理工具
> - 廣泛的社群支援

### 🎯 專案特色

- **自動服務註冊**：應用程式啟動時自動註冊到 Zookeeper
- **動態服務發現**：實時發現服務實例變化
- **負載均衡**：內建輪詢負載均衡策略
- **連接池管理**：優化的 HTTP 連接池配置
- **健康檢查**：完整的應用程式健康監控

## 技術棧

### 核心框架
- **Spring Boot 3.4.5** - 快速建構 Spring 應用程式的框架
- **Spring Cloud 2024.0.2** - 微服務架構的完整解決方案
- **Spring Cloud Zookeeper Discovery** - Zookeeper 服務發現整合

### 開發工具與輔助
- **Apache HttpClient 5** - 高效能的 HTTP 客戶端
- **Apache Zookeeper 3.9.2** - 分散式協調服務
- **Lombok** - 減少 Java 樣板程式碼
- **Joda Money** - 貨幣處理工具庫
- **Apache Commons Lang3** - 常用工具類庫

## 專案結構

```
zk-customer-service/
├── src/
│   ├── main/
│   │   ├── java/tw/fengqing/spring/springbucks/customer/
│   │   │   ├── CustomerServiceApplication.java    # 主應用程式類別
│   │   │   ├── CustomerRunner.java                # 應用程式啟動執行器
│   │   │   ├── model/                             # 資料模型
│   │   │   │   ├── Coffee.java                    # 咖啡商品模型
│   │   │   │   ├── CoffeeOrder.java               # 訂單模型
│   │   │   │   ├── NewOrderRequest.java           # 新訂單請求模型
│   │   │   │   └── OrderState.java                # 訂單狀態列舉
│   │   │   └── support/                           # 支援類別
│   │   │       ├── MoneyDeserializer.java         # 貨幣反序列化器
│   │   │       └── MoneySerializer.java           # 貨幣序列化器
│   │   └── resources/
│   │       └── application.properties             # 應用程式設定檔
│   └── test/
│       └── java/tw/fengqing/spring/springbucks/customer/
│           └── CustomerServiceApplicationTests.java
├── pom.xml                                        # Maven 專案配置
└── README.md                                      # 專案說明文件
```

## 快速開始

### 前置需求
- **Java 21** 或更高版本
- **Maven 3.6** 或更高版本
- **Apache Zookeeper 3.9.2** 或更高版本
- **Docker**（可選，用於快速啟動 Zookeeper）

### 安裝與執行

1. **啟動 Zookeeper 服務：**
```bash
# 使用 Docker 啟動 Zookeeper（推薦）
docker run -d --name spring-zookeeper \
  -p 2181:2181 \
  -e ZOOKEEPER_CLIENT_PORT=2181 \
  apache/zookeeper:3.9.3

# 或使用本地安裝的 Zookeeper
# 確保 Zookeeper 在 localhost:2181 運行
```

2. **克隆此專案：**
```bash
git clone <repository-url>
cd zk-customer-service
```

3. **編譯專案：**
```bash
mvn clean compile
```

4. **執行應用程式：**
```bash
mvn spring-boot:run
```

### 驗證部署

應用程式啟動後，您應該看到以下日誌：
```
INFO --- DiscoveryClient: org.springframework.cloud.client.discovery.composite.CompositeDiscoveryClient
INFO --- Tomcat started on port [隨機端口] (http)
INFO --- Started CustomerServiceApplication in X.XXX seconds
```

## 進階說明

### 環境變數
```properties
# Zookeeper 連接設定
spring.cloud.zookeeper.connect-string=localhost:2181

# 應用程式設定
spring.application.name=customer-service
server.port=0

# 健康檢查設定
management.endpoint.health.show-details=always
```

### 設定檔說明
```properties
# application.properties 主要設定
spring.application.name=customer-service          # 服務名稱
server.port=0                                     # 隨機端口分配
spring.cloud.zookeeper.connect-string=localhost:2181  # Zookeeper 連接字串
management.endpoint.health.show-details=always    # 顯示詳細健康資訊
```

### 重要程式碼區塊說明

#### 1. HTTP 客戶端配置
```java
@Bean
public HttpComponentsClientHttpRequestFactory requestFactory() {
    // 建立連接池管理器，設定最大連接數和每路由連接數
    PoolingHttpClientConnectionManager connectionManager =
            new PoolingHttpClientConnectionManager();
    connectionManager.setMaxTotal(200);           // 最大總連接數
    connectionManager.setDefaultMaxPerRoute(20);  // 每路由最大連接數

    // 建立自定義 HTTP 客戶端，包含連接池和閒置連接清理
    HttpClient httpClient = HttpClients.custom()
            .setConnectionManager(connectionManager)
            .evictIdleConnections(TimeValue.ofSeconds(30))  // 30秒清理閒置連接
            .disableAutomaticRetries()                      // 停用自動重試
            .build();

    return new HttpComponentsClientHttpRequestFactory(httpClient);
}
```

#### 2. 負載均衡 RestTemplate
```java
@LoadBalanced  // 啟用負載均衡功能
@Bean
public RestTemplate restTemplate(RestTemplateBuilder builder) {
    return builder
            .setConnectTimeout(Duration.ofMillis(100))   // 連接超時 100ms
            .setReadTimeout(Duration.ofMillis(500))      // 讀取超時 500ms
            .requestFactory(this::requestFactory)        // 使用自定義請求工廠
            .build();
}
```

#### 3. 服務發現與調用
```java
private void readMenu() {
    // 使用泛型類型參考來處理複雜的 JSON 反序列化
    ParameterizedTypeReference<List<Coffee>> ptr =
            new ParameterizedTypeReference<List<Coffee>>() {};
    
    // 透過服務名稱調用，LoadBalancer 會自動選擇實例
    ResponseEntity<List<Coffee>> list = restTemplate
            .exchange("http://waiter-service/coffee/", HttpMethod.GET, null, ptr);
    
    // 處理回應結果
    list.getBody().forEach(c -> log.info("Coffee: {}", c));
}
```

## 參考資源

- [Spring Cloud Zookeeper 官方文件](https://spring.io/projects/spring-cloud-zookeeper)
- [Apache Zookeeper 官方文件](https://zookeeper.apache.org/)
- [Spring Cloud LoadBalancer 文件](https://spring.io/projects/spring-cloud-loadbalancer)
- [Apache HttpClient 5 文件](https://hc.apache.org/httpcomponents-client-5.3.x/)

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目 | 說明 | 建議做法 |
|------|------|----------|
| Zookeeper 版本 | 版本相容性 | 使用 3.4.x 或 3.9.x 穩定版本 |
| 服務發現 | CAP 理論考量 | 了解 Zookeeper 的 CP 特性 |
| 網路通訊 | 連接池管理 | 適當配置連接池參數 |
| 健康檢查 | 監控機制 | 啟用詳細健康檢查 |

### 🔒 最佳實踐指南

#### 1. Zookeeper 部署建議
- **生產環境**：使用至少 3 個節點的叢集
- **網路隔離**：避免跨機房部署，減少網路延遲
- **監控告警**：設定 Zookeeper 叢集監控

#### 2. 服務發現最佳實踐
- **服務命名**：使用有意義的服務名稱
- **健康檢查**：確保服務健康檢查機制正常
- **降級策略**：實作服務降級機制

#### 3. HTTP 客戶端優化
- **連接池配置**：根據實際負載調整連接池大小
- **超時設定**：設定合理的連接和讀取超時
- **重試機制**：實作適當的重試策略

### 🚨 常見問題與解決方案

#### 1. Zookeeper 連接失敗
```bash
# 檢查 Zookeeper 服務狀態
docker ps | grep zookeeper

# 檢查連接
telnet localhost 2181
```

#### 2. 服務發現失敗
```properties
# 檢查服務註冊狀態
# 使用 Zookeeper CLI 工具
zkCli.sh -server localhost:2181
ls /services
```

#### 3. 負載均衡問題
```java
// 確保 RestTemplate 有 @LoadBalanced 註解
@LoadBalanced
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

## 授權說明

本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

## 關於我們

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。

## 聯繫我們

- **FB 粉絲頁**：[風清雲談 | Facebook](https://www.facebook.com/profile.php?id=61576838896062)
- **LinkedIn**：[linkedin.com/in/chu-kuo-lung](https://www.linkedin.com/in/chu-kuo-lung)
- **YouTube 頻道**：[雲談風清 - YouTube](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- **風清雲談 部落格**：[風清雲談](https://blog.fengqing.tw/)
- **電子郵件**：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

---

**📅 最後更新：2025-08-12**  
**👨‍💻 維護者：風清雲談團隊**

---

> 💡 **學習建議**
> 
> 1. **先了解基礎概念**：熟悉微服務架構和服務發現的基本概念
> 2. **動手實作**：按照快速開始指南實際部署和測試
> 3. **深入學習**：閱讀相關的技術文件和最佳實踐
> 4. **實戰應用**：將學到的知識應用到實際專案中
