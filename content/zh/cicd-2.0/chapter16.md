---
title: 'Chapter 16 研發推動的DevOps'
description: 'Chapter 16 研發推動的DevOps'
position: 110
category: 持續交付2.0：實務導向的DevOps
menuTitle: 'Chapter 16'
contributors: ['cshs108']
---

## 16.3 第二階段：DevOps轉型

- 透過敏捷101的方式，對於時程預估、開發團隊的合作、測試流程都有一定的成長，團隊開始朝向小批量生產的方式開發，並且開始有比較頻繁的成果發佈(ex.兩週一次)
- 從過去的分支開發、合併主線內容到分支、集中測試，改為主線開發、分支發布的城際快線開發模式
    - 在wiki上可以知道每個發布的時間點，以及對應的發布列表
    - 客戶可以隨時提交需求
    - 需求列表中，不但有客戶要求，還包含自我優化需求排程

### 16.3.1 與維運人員的衝突

- 因為頻繁的發布，引發維運人員的憂慮
    - 過去測試時間那麼長，上線的系統還是會出現各種問題，現在測試的時間縮短後(測試左移，導致最後上線前測試時間縮短)，那上線一定會更容易出問題
    - 我會被累死，部署到多台主機，以前三個月加班一次，現在變成兩週就要加班一次
- 透過讓維運人員參與團隊日常的運作，增加透明度
    - 開發負責人為維運人員詳細講解目前使用的研發流程模式，讓他了解目前對於程式碼品質的保障方式
    - 邀請維運人員參與開發團隊活動，參加日常工作的討論ex.參與站立會議、參與團隊衝刺週期後的回顧會議
    - 將維運人員加入開發團隊的工作溝通群中，以便隨時了解運作狀況，這維運人員不再視為開發團隊是一個看不到內部的「黑盒」
- 針對維運需求進行架構調整，讓維運人員的工作更有效率(過去對於維運的需求都以有空再來處理而忽悠過去，現在變成一個合作夥伴，一起讓讓部署過程可以更自動化)

### 16.3.2 高頻部署發布中的具體障礙

- 由於歷史共業，各模組的部署方式不一致，導致不利於統一維運
- 部分模組在部署時，維運人員需要手動創建某個目錄，備份程式運行時產生的臨時數據
- 同樣的模組在不同機器上的部署位置都有差別
- 同樣的模組在不同機器上對外的接口策略也不同
- 程式碼是用絕對路徑而非相對路徑
- 部署操作文件是由開發在部署前編寫(代表每次可能都不一樣，導致無法透過自動化反覆執行)，維運人員根據文件說明操作
- 開發環境的部署方式跟正式環境的部署方式不同
- 測試代碼存在QA的儲存庫，而不是跟著專案跑，導致只有QA才有辦法執行測試
- 開發環境、測試環境共用，導致髒資料議題
- 自動化測試覆蓋率不足，導致測試人員仍然需要執行很多手工回歸測試(每次上版都要手動測試全系統)

### 16.3.3 整體解決方案的設計

- 自動化測試策略的調整：
    - 把測試從QA擴展到全開發，對於開發人員進行單元測試框架的培訓
    - 減少不同層次間的重複測試，認知「通過低層次的測試來驗證很難在高層次驗證的測試」，找到最適合測試的階層，不是所有的測試都是交給QA執行整合測試

![截圖 2022-10-27 上午12.01.46.png](https://cshs108.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc2b96270-0057-4f89-b548-9f18c51fa8bc%2F%25E6%2588%25AA%25E5%259C%2596_2022-10-27_%25E4%25B8%258A%25E5%258D%258812.01.46.png?table=block&id=5a4563c9-dc7d-4743-9b14-682c767a975a&spaceId=5d2d13ed-dba9-47b9-be73-4ea525461379&width=2000&userId=&cache=v2g)

- 自動化測試的便捷性
    - 透過配置文件的方式讓不同的開發可以共用一個測試環境(ex.只要換不同的配置文件，系統就可以上到某一台測試主機，要換到另一台主機，只要更換配置文件不需要修改程式碼就可以遷移過去)
- 測試代碼同源
    - 把測試代碼放到專案裡面，而不是QA的儲存庫，這樣任何人都可以去執行測試
- 配置管理優化
    - 代碼庫結構
        - 程式資料夾有統一的結構，模組相關的就在該模組資料夾下(ex.該模組的測試，是放在該模組的test資料夾內，而不是整個系統的測試全部放一起)，該模組的config就放在該模組下
        - ex.單元測試就放在「模組a/test/unittest/測試案例1」
        - 會有output的資料夾，提供維運人員部署使用
    - 產出物的標準化與版本管理
        - 持續整合產出的成品，透過建構號(build_id)作為成品的唯一識別
        - 取代手工自行編譯
- 軟件包管理
    - 只有經過一系列的自動化測試，並經過測試人員手工驗證後，才在臨時構建成品庫中標記為「符合品質標準」，當要獲取某個版本部署時，只能從臨時構建成品庫中選擇那些符合品質標準的版本放入產品發布庫

![截圖 2022-10-27 上午12.19.44.png](https://cshs108.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0a155aa9-f506-4f52-99a9-aa9bf917e292%2F%25E6%2588%25AA%25E5%259C%2596_2022-10-27_%25E4%25B8%258A%25E5%258D%258812.19.44.png?table=block&id=5caf7c63-192b-4518-be0a-026468f6fccf&spaceId=5d2d13ed-dba9-47b9-be73-4ea525461379&width=2000&userId=&cache=v2)

### 16.3.4 DevOps階段的團隊改變

- 完整的跨功能團隊，維運積極參與團隊的日常工作迭代會議
- 所有內容都做版本控制，包括原始碼、測試程式、各類環境的配置、相關的打包及安裝腳本，以及一些數據
- 所有環境標準化管理，可以一鍵式準備好測試環境
- 建立完整的部署流水線，可以一鍵式發佈到多種部署環境

![截圖 2022-10-27 上午12.20.45.png](https://cshs108.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbe6fbfd2-7189-4f7c-b915-06f5c7d4cdf5%2F%25E6%2588%25AA%25E5%259C%2596_2022-10-27_%25E4%25B8%258A%25E5%258D%258812.20.45.png?table=block&id=09d3c91c-77ee-431b-ace9-2dea3aafa134&spaceId=5d2d13ed-dba9-47b9-be73-4ea525461379&width=2000&userId=&cache=v2)

## 16.4 小結

- 所有改進都是一種一連串的連續流程，從明確目標、診斷問題、解決方案、持續運營優化
- 過程中抱持著持續學習的心態

![截圖 2022-10-27 上午12.25.38.png](https://cshs108.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F794b492a-78c7-4de5-9560-203bbc6e462c%2F%25E6%2588%25AA%25E5%259C%2596_2022-10-27_%25E4%25B8%258A%25E5%258D%258812.25.38.png?table=block&id=dd6bb26b-9b31-4f97-b245-1f1897207537&spaceId=5d2d13ed-dba9-47b9-be73-4ea525461379&width=1570&userId=&cache=v2)