---
title: 'Chapter 07 部署流水線原則與工具設計 (7-5 ~ 7-7)'
description: 'Chapter 07 部署流水線原則與工具設計'
position: 101
category: 持續交付2.0：實務導向的DevOps
menuTitle: 'Chapter 07_5'
---

## 第7章. 部署流水線原則與工具設計 (7-5 ~ 7-7)

### 7.5 企業製品庫的管理

企業製品庫是部署流水線工具鏈中企業的受信源之一，也是企業信息管理中的一個重要節 <br>
點。只有通過安全驗證的軟體包才會被納入製品庫，並且安全驗證部門也應定期對存儲的 <br>
對存儲的內容作安全掃描及清理。

#### 7.5.1 製品庫的分類

![image](https://user-images.githubusercontent.com/54253518/166915657-c5035a37-caea-4037-88f2-4297753b96bb.png)

1. 臨時軟體包庫(A) <br>
用於存儲團隊開發並通過流水部屬線生成代碼的所有軟體包。

2. 正式軟體包庫(B) <br>
用於存儲通過流水線部署且經過安全驗證，被確認能夠發布到生產環境或使用者的軟體包。

3. 外部軟體包庫(C) <br>
指該軟體包的代碼並非由團隊管理或維護，但在開發中所使用到的其他軟體包。這些軟 <br>
體包通過互聯網或是第三方取得，亦將其存儲在製品庫中。 <br>
外部軟體包一般存儲的形式可能包含3種： <br>
(1) 以二進制的方式保存。 <br>
(2) 以代碼副本的方式保存。 <br>
(3) 以外部連結地址的方式保存。 <br>

4. 臨時鏡像庫(D)、正式鏡像庫(E)、外部鏡像庫(F) <br>
基本上同軟體包庫，只是以鏡像的方式作存儲。

#### 7.5.2 製品庫的管理原則

製品庫中，每個製品都應該有標示，並且連同其來源、組成的部件以及用途等，一起保 <br>
存為該製品的信息。所有製品都要能夠追溯至源頭， 包括臨時製品庫中的製品。

### 7.6 多種多樣的部署流水線

#### 7.6.1 多組件的部署流水線

若一個軟體產品由多個組件建構而成，每個組件都有獨自的代碼倉庫，並且每個組件由 <br>
一個單獨的團隊負責開發與維護，整個產品的部署流水線的設計通常與下圖相似。

![image](https://user-images.githubusercontent.com/54253518/166899205-b31d710f-6249-41a5-ba19-efccb1121fde.png)

每個組件的部署流水線成功以後，都能觸發下游的產品集成部署流水線。而這個集成 <br>
部署流水線的打包階段，會自動從軟體包庫中獲取每個組件最近成功的軟體包，然後 <br>
對其進行產品打包，再觸發集成部署流水線的後續階段。

#### 7.6.2 個人部署流水線

每名工程師創建了自己專屬的部署流水線，用於個人在未推送代碼到團隊倉庫之前的 <br>
使用。個人部署流水線並不會部署到團隊共同擁有的環境中，而是僅覆蓋個人開發環 <br>
節。

![image](https://user-images.githubusercontent.com/54253518/166899696-d76c146d-e533-4ad3-b56a-98640ff7761f.png)

工程師通過部署流水線的模板功能，複製一份團隊部署的副本。並僅保留兩個階段(提 <br>
交建構、次級建構)的內容。令工程師能夠監聽自己代碼倉庫的變化，並且自動化去觸 <br>
發。當開發人員提交代碼到個人倉庫時，都會自動觸發個人專屬的部署流水線。

這樣做的好處有3個：
1. 個人部署流水線與團隊的部署流水線共享建構及測試環境。
2. 保證每個工程師都能利用到相同的測試資源，加快個人檢驗的速度。
3. 個人部署流水線的測試用例與團隊的部署流水線的驗證集合相同，若建構失敗，則容易定位問題。

#### 7.6.3 部署流水線的不斷演進

截止到2018年4月，GoCD的社區版本每月會發布一次正式版，而其團隊的複雜的部署流 <br>
水線設計也已演變如下圖所示：

![image](https://user-images.githubusercontent.com/54253518/166899792-fcad6ccd-9287-47ca-81d3-6239c603d261.png)

構置Linux包這個部署流水線中，包含兩個階段。第一個階段是build-no_server。多 <br>
個任務並行執行，構置組成Server 所需的多個Jar包，也並行執行Java測試用例和 <br>
JavaScript單元測試用例。這體現了部署流水線盡量並行化原則。第二個階段是 <br>
build-server，使用經第一個階段己初步驗證通過的多個Jar包組裝成Sever包。

Linux驗收測試這個部署流水線中，也包含兩個階段。第一個階段是運行高優先級的功 <br>
能測試，第二個階段是對插件部分的自動化功能測試，這體現了部署流水線的快速反饋 <br>
優先原則。

而在後續的各類測試(如驗收測試、回歸測試或者功能測試)中，被測試的二進制包均來 <br>
自前面各部署流水線的產出物，而且確保其使用同一代碼版本。

### 7.7 為開發者建置自助式工具

優秀的互聯網公司採用了一種工具平台的設計理念，即為開發工程師設計他們認為好用 <br>
的工具。這種方式要求創建強大的工具平台，能夠很好地支持開發工程師做產品服務。

例如 Facebook，開發人員可以通過他們內部平台看到自己的代碼已經發佈到哪個階段 <br>
，有多少用戶在使用。開發工程師在不需要任何人幫助的情況下，就能夠了解他的代碼 <br>
已經發佈到哪個階段了。

![image](https://user-images.githubusercontent.com/54253518/166907831-bd62ef0a-cc80-453a-b548-d91f58dde85f.png)

例如電商公司 Etsy，開發工程師可以查看到自上次生產部署以後，每次的代碼變更數 <br>
量，並且非常方便地查找代碼差異。

![image](https://user-images.githubusercontent.com/54253518/166907867-e504bada-70cf-4ca2-a575-6cadb885a3d0.png)
