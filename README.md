# shrine-img

「日本神社寺廟散策地圖」單元的照片與散策圖，供 [CHRIS OS](https://chrisincite.github.io/shrine/) 引用。

分離出來的理由：GitHub Pages 對**每個站台**有 1 GB 硬上限，而主 repo
`chrisincite.github.io` 是全站共用那 1 GB。照片預估最終約 550 MB，
放主 repo 會吃掉一半以上的預算，也讓主 repo 的 clone 變重。

- 供稿端：`~/Documents/shireTemple/`（源檔、research、routes）
- 引用端：`scripts/build.py` 的 `IMG_BASE` 常數
- 命名沿用 `<編號>-<slug>-<序號>.jpg`，散策圖為 `-map.jpg`

## 為什麼是 baseline JPEG 不是 WebP

2026-09-15 全庫從 WebP 轉成 baseline JPEG。Safari／ImageIO 沒有 WebP 的硬體
解碼路徑，而且 `kCGImageSourceSubsampleFactor` 對 WebP 完全無效——不管顯示多小，
大圖永遠被全解析度硬解。本機實測同一張 1200×1600：

| 格式 | 解碼時間 |
|---|---|
| baseline JPEG | 8.1 ms |
| HEIC | 20 ms |
| **progressive JPEG** | **17.6 ms** |
| WebP | 30.2 ms |

一頁 14 張照片，等於 420 ms vs 110 ms 的差別（iPhone 上再乘 3–5 倍）。

**一定要 baseline，不能 progressive**：progressive JPEG 慢到跟 WebP 差不多，
換了等於白換。轉檔指令：

```sh
ls *.webp | xargs -P 8 -I{} sh -c \
  'magick "$1" -quality 85 -sampling-factor 4:2:0 -strip -interlace none "${1%.webp}.jpg"' _ {}
```

`-quality 85` 是實測出來的平衡點：相對原 WebP 體積 1.31 倍、RMSE 0.0147
（肉眼無差），全庫 453 MB → 594 MB，仍在 Pages 的 1 GB 內。
