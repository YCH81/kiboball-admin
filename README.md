# 主題日後台

## 開啟方式

在專案根目錄執行，然後開 <http://localhost:8811>：

```bash
python3 -m http.server 8811 --directory admin
```

不要直接以 `file://` 雙擊開啟——瀏覽器會因 CORS 拒絕從 `file://` 頁面載入
ES module，頁面會空白。

後台**不架在網路上**：Supabase 的閘道會把 Edge Function 回傳的 HTML 一律改成
`text/plain`（避免有人拿 supabase.co 網域架釣魚頁），所以無法從後端供應網頁；
放在本機也讓後台不對外暴露。

## 權限

寫入需要登入，且該帳號必須在 `admins` 表內。頁面只帶公開的 anon key，
外流不會取得寫入權。新增管理者：

```sql
insert into admins (user_id, email)
select id, email from auth.users where email = '<對方的 email>';
```

## 主題日從哪來

中職沒有逐場的主題日資料源，各球團自己公告：

| 球團 | 型態 | 匯入建議 |
|---|---|---|
| 統一獅 | 頁面內嵌 JSON | 可 |
| 中信兄弟 | 伺服器端輸出的公告內文 | 可 |
| 台鋼雄鷹 | 僅 FB 貼文 | 否，人工建立 |
| 味全龍 | 文章式散文 | 否，人工建立 |
| 富邦悍將 | 整頁只有一張圖 | 否，人工建立 |
| 樂天桃猿 | 伺服器未提供中介憑證 | 否，人工建立 |

「讀取建議」只解析、不寫入，確認後按「帶入表單」再儲存。
解析器在 `supabase/functions/_shared/themes.ts`，球團改版時改那裡。
