# shrine-img

「日本神社寺廟散策地圖」單元的照片與散策圖，供 [CHRIS OS](https://chrisincite.github.io/shrine/) 引用。

分離出來的理由：GitHub Pages 對**每個站台**有 1 GB 硬上限，而主 repo
`chrisincite.github.io` 是全站共用那 1 GB。照片預估最終約 550 MB，
放主 repo 會吃掉一半以上的預算，也讓主 repo 的 clone 變重。

- 供稿端：`~/Documents/shireTemple/`（源檔、research、routes）
- 引用端：`scripts/build.py` 的 `IMG_BASE` 常數
- 命名沿用 `<編號>-<slug>-<序號>.webp`，散策圖為 `-map.webp`
