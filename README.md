# shrine-img

「日本神社寺廟散策地圖」單元的照片與散策圖，供 [CHRIS OS](https://chrisincite.github.io/shrine/) 引用。

分離出來的理由：主 repo `chrisincite.github.io` 的 clone 不該被幾百 MB 照片拖重。

**照片不從 GitHub Pages 供應**，見下節。

- 供稿端：`~/Documents/shireTemple/`（源檔、research、routes）
- 引用端：`scripts/build.py` 的 `IMG_BASE` 常數
- 命名沿用 `<編號>-<slug>-<序號>.webp`，散策圖為 `-map.webp`

## 為什麼照片走 Cloudflare 不走 GitHub Pages

2026-09-15 量到的：台灣（HiNet）到 GitHub Pages 的 Fastly 新加坡節點
（`x-github-edge-region: southeastasia`）**丟包率 14%**，同時段到 Cloudflare 0%。
同一個 681 KB 檔案各打 8 次：

| | 吞吐量中位數 | 範圍 |
|---|---|---|
| GitHub Pages | ~36 KB/s | 20.8–919 KB/s |
| Cloudflare | ~556 KB/s | 345–601 KB/s |

Cloudflare 的 RTT 還比較高（221 vs 128 ms），照樣快 15 倍——**距離不是問題，丟包才是**。
TCP 吞吐量約 `MSS / (RTT × √loss)`，14% 丟包算出來就是 43 KB/s，跟實測吻合。

雪上加霜的是瀏覽器對同一 origin 只開**一條 HTTP/2 連線**，整頁 14 張圖全在那條上多工，
丟包的損失無法靠多開連線攤掉。實測單一 HTTP/2 連線抓 6 張要 140 秒，6 條獨立連線只要 22 秒。

所以照片改由 Cloudflare Pages 供應，`IMG_BASE` 指到那邊。GitHub 這個 repo 只當版本庫。

## 為什麼是 WebP

因為瓶頸是傳輸不是解碼。WebP 全庫 453 MB，同畫質的 baseline JPEG 是 594 MB（+31%）。

曾經為了解碼速度全轉成 JPEG（Safari／ImageIO 沒有 WebP 硬體解碼路徑，實測同一張
1200×1600：baseline JPEG 8.1 ms、WebP 30.2 ms），但那是誤判——一頁 14 張圖的解碼差距
只有 0.3 秒，傳輸差距是好幾秒。在頻寬受限的情況下，檔案小才是對的，已轉回 WebP。

解碼數字本身仍然成立，如果哪天頁面在頻寬充足下還是卡，再回來看這條。
