我要請你幫我上架一個網站。

背景：我手上有一個用AI工具（TAVA AI、Tooo.ai、Lovart.ai、Gemini、ChatGPT等）做好、已經打包成zip（或tar.gz）的靜態網站，裡面有index.html跟圖片等素材。我有一個自動化系統可以幫我把這個網站變成正式、可公開瀏覽的網址，你只要照著下面步驟做就好。

## 你要做的事

1. 先問我：「打包好的網站檔案在哪裡？」——確認檔案的實際路徑後再繼續，不要用假設的檔名。
2. 用下面這個curl指令上傳到我的上架系統（`https://event.bymyway.com/curation/upload?key=VoARDsbU85r2fhzJPCwhLv-xVm6pHTxZ`是專屬這次任務的通關網址，直接用即可，不用登入帳密）：

```bash
curl -s -F "package=@<你剛確認到的真實檔案路徑>" "https://event.bymyway.com/curation/upload?key=VoARDsbU85r2fhzJPCwhLv-xVm6pHTxZ" -o /tmp/curation_upload_resp.html -w "HTTP %{http_code}\n"
grep -o "已收到「[^」]*」" /tmp/curation_upload_resp.html
```

3. 上傳成功後，系統會在背景自動處理（通常10~20秒）：解壓縮、建立GitHub網站、上架、收錄進店家名冊。你不用做任何事，也不用寫程式碼去確認進度——**只要把下面這句話原封不動告訴我**：

   「已上傳，系統正在背景自動處理，完成後教練會收到Telegram通知附上網址，通常10~20秒內完成。」

4. 如果curl指令回傳的HTTP狀態碼不是200，或grep不到「已收到」字樣，請把完整的錯誤訊息告訴我，不要自己瞎猜原因。

## 重要原則

- 你不需要、也不應該自己動手建立GitHub repo、寫程式碼做網站上架——那些背景自動化都已經做好了，你只要負責把檔案送到上傳網址就完成任務。
- 如果我沒有給你打包好的檔案，不要編造一個假的zip來測試，直接問我檔案在哪裡。
- 完成後不用主動幫我發FB貼文或修改任何其他網站，那些是我這邊另外的人工/自動化流程，跟你的任務無關。
