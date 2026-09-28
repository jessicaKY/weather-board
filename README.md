# weather-board

氣象看板專案。

## 本機

```bash
pnpm install
pnpm dev
```

開 http://localhost:3000 。

環境變數見 [`.env.example`](./.env.example)（`CWA_API_KEY`、`ANTHROPIC_API_KEY`、Discord 相關）。

## 天氣 API

`GET /api/weather?city=Taipei`，資料源為中央氣象署（CWA）；契約見 [`API-CONTRACT.md`](./API-CONTRACT.md)（`current` / `hourlyForecast` / `dailyForecast`）。

Discord 推播另走 Open-Meteo（`lib/weather-source.js`），不經這支 route。

## 天氣小幫手（Agent）

- UI：`components/weather/weather-agent-panel.jsx`，props `{ city }`，與首頁同一 `city` state。
- API：`POST /api/agent`（body `{ city, question }`）；契約見 [`API-CONTRACT.md`](./API-CONTRACT.md) 文末。
- 有 `ANTHROPIC_API_KEY` → Claude（可 Tool Calling）；沒 key／失敗 → 規則備援，不壞。
- 未知城市 soft reply（不硬 400）；面板 25s 逾時、競態不空白。
- 快捷問題：今天要帶傘嗎？／晚上會冷嗎？／適合戶外運動嗎？／未來幾小時會下雨嗎？

## Discord 推播

降雨機率偏高時，透過 Webhook 把天氣摘要推到 Discord；Vercel Cron 每天台北時間 08:00／17:00 各跑一次（預設台北）。

線上要在 Vercel 設定 `DISCORD_WEBHOOK_URL`、`CRON_SECRET`（其餘選填見 [`.env.example`](./.env.example)）。Webhook URL 與 secret **不要**寫進 git、截圖或投影片。

本機把同樣變數放進 `.env.local` 即可；細節與測試方式見程式註解。
