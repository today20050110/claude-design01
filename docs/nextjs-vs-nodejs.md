# 使用 Next.js 之後，還需要 Node.js 之類的技術嗎？

## 短答

**Node.js 一定會用到，但通常不需要「另外再寫一個 Node/Express 後端」。**

## 1. Node.js 是執行環境，不是可以跳過的選項

Next.js 本身就是一個跑在 Node.js 上的框架。`npm run dev`、`next build`、
SSR/SSG 的渲染、Server Components 的執行，全部都由 Node.js 執行。

「用了 Next.js 就不用 Node.js」的說法不成立，它比較像是
「用了 Django 就不用 Python 嗎」。

**例外只有部署端**：如果整個專案是純靜態輸出（`output: 'export'`），
或把 Route Handler 都跑在 Edge Runtime（Cloudflare Workers、Vercel Edge），
那**線上環境**可以沒有 Node.js。但開發與 build 階段仍然需要。

## 2. 不需要的是「額外的 Express / Nest 後端」

Next.js 的 App Router 已內建後端能力：

| 需求 | Next.js 內建方案 |
| --- | --- |
| REST API 端點 | Route Handlers（`app/api/*/route.ts`） |
| 表單提交、寫入資料 | Server Actions |
| 資料庫查詢 | Server Components 直接 `await` 查詢 |
| 認證 | middleware + Auth.js / Clerk |
| 檔案上傳 | Route Handler + S3 / R2 SDK |

中小型專案（後台系統、SaaS、電商、內容站）用這些就夠了。
多開一個 Express 只會增加部署成本與型別同步的負擔。

## 3. 什麼時候才真的該拆出獨立後端

- 需要 **WebSocket / 長連線**（Next.js 的 serverless 環境不適合）
- 需要 **背景任務、排程、queue worker**（請求結束後還要繼續跑的工作）
- 後端要**同時服務多個前端**（Web + iOS + Android + 第三方 API）
- 團隊已有既存的 Java / Go / Python 後端，Next.js 只當 BFF 或純前端
- 有重運算、長時間執行的工作（超過 serverless 的執行時間上限）

此時常見架構是：Next.js 負責 UI 與 BFF 層，後面接一個獨立的 API service。

## 建議

起新專案時**先只用 Next.js**，把 Route Handlers 和 Server Actions 當後端用。
等真的撞到上述限制（尤其是 WebSocket 或背景任務）再拆出去，
不要一開始就預先切分。
