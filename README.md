# NCKU Modular Course - FinTech (成大金融科技模組化課程)

這是一個為「成功大學金融科技模組化課程」開發的綜合型全端專案。採用**前後端分離**、**服務微型化**以及**容器化部署**的設計，將不同功能模組與服務進行解耦，以達到易於開發、維護與擴展的目的。

## 專案架構與我的努力成果 (Project Architecture & Efforts)

為了讓系統高內聚、低耦合，我將本專案拆分為四個主要的子服務，涵蓋了前端展示、登入獨立微服務以及主應用程式後端：

### 1. 課程主要前端服務 (`finTech_front_end/`)

- **技術棧**：Vue.js 3 + Vite + Highcharts
- **功能說明**：負責呈現課程 Day 1 到 Day 3 的金融科技相關數據與使用者介面。
- **我的努力與貢獻**：
  - **高度模組化元件**：獨立出共用元件如導覽列 (`Navbar.vue`) 與等待畫面 (`Wait.vue`)。
  - **課程視圖分離**：針對課程的不同天數開發了獨立的視圖（`Day1.vue`, `Day2.vue`, `Day3_1.vue`, `Day3_2.vue`），使各模組功能互不干擾，方便後續擴展或單獨抽換。
  - **數據視覺化整合**：透過 `utils/highcharts-setup.js` 完美整合 Highcharts 圖表套件，將複雜的金融數據轉化為易懂的圖表前端展示。
  - **容器化設定**：撰寫輕量化的 Dockerfile，讓前端環境統一。

### 2. 獨立登入前端 (`login_account/`)

- **技術棧**：Vue.js 3 + Vite
- **功能說明**：專注於處理使用者登入與註冊介面的獨立前端服務。
- **我的努力與貢獻**：
  - **前端微服務概念**：將登入介面（`Login.vue`）從主要業務邏輯中完全抽離，這在權限管理更新或介面大改版時，不需要重新編譯整個主應用系統。
  - 獨立配置路由，確保登入流程的封閉性與安全性。

### 3. 專屬驗證微服務後端 (`login/`)

- **技術棧**：Python + Django
- **功能說明**：負責帳號管理與安全驗證的後端。
- **我的努力與貢獻**：
  - **獨立 Database 與 App**：架設獨立的 Django 專案，專門處理 `login_manager` 驗證邏輯與獨立的 SQLite 庫。
  - **分散式驗證系統架構**：提供專屬 API 供 `login_account` 前端呼叫。讓帳號密碼與驗證邏輯與主業務切割，實踐了後端微服務設計的精神，大幅提升整體專案的資訊安全性。

### 4. 課程主業務邏輯後端 (`NCKU_Modular_Course/`)

- **技術棧**：Python + Django
- **功能說明**：處理課程核心業務邏輯的後端 API，負責接收前端請求、運算金融模型並回傳資料。
- **我的努力與貢獻**：
  - **嚴格的 Django App 切割**：我將 `day1`, `day2`, `day3`, `sum` 以及 `authentication` 切分為五個不同的 Django 子應用程式（App）。職責高度分離，使龐大的程式碼目錄變得非常有條理且易於尋找特定邏輯。
  - **資料流串接**：完成這五個模組的 Views, Models, 與 URLs 設計，確保與前端 `finTech_front_end` 在不同課程主題的資料串接順暢。
  - **靜態檔案與樣式管理**：在 `static/` 以及 `templates/` 中完善配置了早期或備用的 CSS/JS/HTML 檔案（如 `day1.css`, `sum.html`），保證多樣化的渲染需求。

## 系統 DevOps 與容器化整合 (Docker & CI/CD)

本專案除了程式碼的架構設計外，我還花了大量心思在環境的統一與部署流程上：

- **Docker Compose 根目錄統籌**：在專案根目錄撰寫了強大的 `docker-compose.yml`，將這 4 個原本獨立的專案（2 個前端、2 個後端）完美串聯，設定好彼此的網路連線與資料夾對映。
- **客製化 Dockerfiles**：每一個子目錄（`finTech_front_end`, `login`, `login_account`, `NCKU_Modular_Course`）內部都包含了各自的 `Dockerfile`。確保任何人拿到這份專案，不管是在 Windows / Mac / Linux 都不用經歷繁瑣的環境設定 (npm install / pip install 等)。

## 如何一鍵啟動專案

1. 確保您的系統已安裝 Docker 與 Docker Compose。
2. 在專案根目錄 (此 `README.md` 所在目錄) 下開啟終端機，執行以下指令：
   ```bash
   docker-compose up --build
   ```
3. 系統將自動平行構建前端與後端的 Image，並依賴順序啟動所有服務的 Container，您就可以在瀏覽器中體驗完整的系統！
