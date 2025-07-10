# Spring Boot 微服務架構實戰 - 咖啡訂單服務 ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

這是一個基於 Spring Boot 3.x 的微服務架構實戰專案，主要展示咖啡訂單管理系統的後端 API 實作。專案採用現代化的 Jakarta EE 標準，結合 JPA、Lombok 等技術，提供完整的 RESTful API 服務。

### 🎯 專案特色

- **現代化技術棧**：採用 Spring Boot 3.4.5 + Java 21 + Jakarta EE
- **完整的 CRUD 操作**：提供咖啡訂單的建立、查詢、狀態更新等功能
- **資料持久化**：使用 JPA + H2 資料庫，支援自動建表
- **RESTful API 設計**：遵循 REST 規範，提供標準化的 HTTP 介面
- **貨幣處理**：整合 Joda Money 處理價格計算，支援多幣別
- **程式碼品質**：使用 Lombok 減少樣板程式碼，提升開發效率

> 💡 **為什麼選擇此專案？**
> - 展示 Spring Boot 3.x 的最新特性與最佳實踐
> - 提供完整的微服務架構實作範例
> - 結合實務需求，適合學習與參考

## 技術棧

### 核心框架
- **Spring Boot 3.4.5** - 現代化的 Java 應用程式框架
- **Spring MVC** - Web 層處理，提供 RESTful API
- **Spring Data JPA** - 資料存取層，簡化資料庫操作
- **Jakarta EE** - 企業級 Java 標準，取代舊版 javax

### 開發工具與輔助
- **Lombok** - 自動生成 getter/setter、builder 等樣板程式碼
- **Joda Money** - 貨幣處理庫，支援精確的價格計算
- **H2 Database** - 內嵌式資料庫，適合開發與測試
- **Maven** - 專案建構與依賴管理工具

## 專案結構

```
complex-controller-demo/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── tw/fengqing/spring/springbucks/waiter/
│   │   │       ├── WaiterServiceApplication.java     # 應用程式啟動類別
│   │   │       ├── controller/                       # 控制器層
│   │   │       │   ├── CoffeeController.java        # 咖啡管理 API
│   │   │       │   ├── CoffeeOrderController.java   # 訂單管理 API
│   │   │       │   └── request/                     # 請求物件
│   │   │       │       └── NewOrderRequest.java     # 新訂單請求
│   │   │       ├── model/                           # 實體模型層
│   │   │       │   ├── BaseEntity.java              # 基礎實體類別
│   │   │       │   ├── Coffee.java                  # 咖啡實體
│   │   │       │   ├── CoffeeOrder.java             # 訂單實體
│   │   │       │   ├── OrderState.java              # 訂單狀態列舉
│   │   │       │   └── MoneyConverter.java          # 貨幣轉換器
│   │   │       ├── repository/                      # 資料存取層
│   │   │       │   ├── CoffeeRepository.java        # 咖啡資料存取
│   │   │       │   └── CoffeeOrderRepository.java   # 訂單資料存取
│   │   │       └── service/                         # 業務邏輯層
│   │   │           ├── CoffeeService.java           # 咖啡業務邏輯
│   │   │           └── CoffeeOrderService.java      # 訂單業務邏輯
│   │   └── resources/
│   │       ├── application.properties               # 應用程式設定檔
│   │       ├── data.sql                             # 初始資料
│   │       └── schema.sql                           # 資料庫結構
│   └── test/                                        # 測試程式碼
├── pom.xml                                          # Maven 專案設定
└── README.md                                        # 專案說明文件
```

## 快速開始

### 前置需求
- **Java 21** - 確保已安裝 JDK 21 或以上版本
- **Maven 3.6+** - 專案建構工具
- **IDE 支援** - 建議使用 IntelliJ IDEA 或 Eclipse

### 安裝與執行

1. **克隆此倉庫：**
```bash
git clone https://github.com/SpringMicroservicesCourse/spring-microservices-practice.git
```

2. **進入專案目錄：**
```bash
cd complex-controller-demo
```

3. **編譯專案：**
```bash
mvn clean compile
```

4. **執行應用程式：**
```bash
mvn spring-boot:run
```

5. **驗證服務啟動：**
```bash
curl http://localhost:8080/order/1
```

## API 使用說明

### 建立新訂單
```bash
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -d '{
    "customer": "Li Lei",
    "items": ["mocha", "latte"]
  }'
```

### 查詢訂單
```bash
curl http://localhost:8080/order/1
```

### 查詢咖啡列表
```bash
curl http://localhost:8080/coffee/
```

## 進階說明

### 環境變數
```properties
# 資料庫設定
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver

# JPA 設定
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

# 應用程式設定
server.port=8080
logging.level.tw.fengqing=DEBUG
```

### 重要程式碼說明

#### 貨幣轉換器 (MoneyConverter.java)
```java
/**
 * JPA 屬性轉換器
 * 用於在資料庫和 Java 物件之間轉換 Money 類型
 * 將金額轉換為最小貨幣單位（分）進行儲存
 */
@Converter(autoApply = true)
public class MoneyConverter implements AttributeConverter<Money, Long> {
    // 實作轉換邏輯
}
```

#### 訂單控制器 (CoffeeOrderController.java)
```java
/**
 * 訂單管理控制器
 * 提供訂單的建立、查詢等 RESTful API
 * 支援 JSON 格式的請求與回應
 */
@RestController
@RequestMapping("/order")
@Slf4j
public class CoffeeOrderController {
    // 控制器實作
}
```

## 錯誤處理說明

### HTTP 狀態碼對應

| 狀態碼 | 說明 | 常見原因 |
|--------|------|----------|
| 200 | 成功 | 正常查詢操作 |
| 201 | 建立成功 | 新訂單建立成功 |
| 400 | 請求錯誤 | 請求體格式錯誤或缺少必要參數 |
| 406 | 不接受 | Accept 頭部與伺服器回應格式不匹配 |
| 415 | 不支援的媒體類型 | Content-Type 不是 application/json |

### 常見錯誤處理

**415 錯誤**：當 Content-Type 不是 `application/json` 時
```bash
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: text/plain" \
  -d "test"
```

**400 錯誤**：當請求體為空時
```bash
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json"
```

**406 錯誤**：當 Accept 頭部不匹配時
```bash
curl -X POST http://localhost:8080/order/ \
  -H "Content-Type: application/json" \
  -H "Accept: text/plain" \
  -d '{"customer": "test", "items": ["latte"]}'
```

## 參考資源

- [Spring Boot 官方文件](https://spring.io/projects/spring-boot)
- [Spring Data JPA 參考手冊](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
- [Jakarta EE 官方網站](https://jakarta.ee/)
- [Lombok 專案頁面](https://projectlombok.org/)

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目 | 說明 | 建議做法 |
|------|------|----------|
| 資料庫連線 | 生產環境資料庫設定 | 使用外部資料庫，避免使用 H2 |
| 安全性 | API 認證與授權 | 實作 Spring Security |
| 效能 | 資料庫查詢最佳化 | 使用適當的索引與查詢策略 |
| 日誌記錄 | 應用程式監控 | 設定適當的日誌級別 |

### 🔒 最佳實踐指南

- **程式碼註解**：在重要的程式碼區塊添加清楚註解，方便團隊成員理解與維護
- **錯誤處理**：實作統一的錯誤處理機制，提供友善的錯誤訊息
- **API 文件**：使用 Swagger 或 OpenAPI 生成 API 文件
- **單元測試**：為重要的業務邏輯撰寫單元測試
- **程式碼品質**：使用 Checkstyle、PMD 等工具確保程式碼品質

### 🚀 開發建議

1. **使用台灣常用的專業用語**，確保溝通順暢且符合本地習慣
2. **遵循 RESTful API 設計原則**，提供直觀的 API 介面
3. **實作適當的資料驗證**，確保資料的完整性與正確性
4. **考慮向後相容性**，避免破壞性的 API 變更

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

**📅 最後更新：2025-07-10**  
**👨‍💻 維護者：風清雲談團隊** 