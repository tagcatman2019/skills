# Video Frame Review - トラブルシューティング

このファイルは、発生した問題と解決方法を記録します。

---

## 🔧 よくある問題と解決方法

### 問題: mp4を `Read` ツールで直接開けない
**症状:** 動画ファイルをReadしても内容を見られない／エラー。
**原因:** Readは画像(PNG/JPG)とPDFのみ対応。動画の直接再生は不可。
**解決方法:** ffmpegでフレーム静止画に書き出してから、そのPNGをReadする（本スキルの本筋）。
**予防策:** 「動画を見る＝フレーム化してから読む」と認識しておく。

---

### 問題: `pdftoppm failed` / 画像変換系が見つからない
**症状:** ReadでのPDF→画像化や一部変換で `pdftoppm not found` 等。
**原因:** 変換ツールが環境に無い。
**解決方法:** 動画フレーム化は **ffmpeg** を使う（pdftoppm не要）。PDFテキストは `pdftotext`。

---

### 問題: `ffmpeg: command not found`
**症状:** STEP4の抽出コマンドが失敗。
**原因:** ffmpeg未インストール or PATH未設定。
**解決方法:** `winget install Gyan.FFmpeg`、または公式DLしてPATHを通す。インストール後にシェルを開き直す。
**予防策:** STEP1で必ず `command -v ffmpeg` を確認。

---

### 問題: yt-dlp の警告 `No supported JavaScript runtime could be found`
**症状:** DL自体は進むが警告が出る／一部フォーマットが取れないことがある。
**原因:** JSランタイム(deno等)が無い。多くの場合DLは成功する。
**解決方法:** 通常は無視可。必要なら `deno` を入れる。フォーマット取得に失敗する場合は `-f "bv*[height<=720]+ba/b[height<=720]"` のように緩めに指定。

---

### 問題: 出力フレームのタイムスタンプ名が「和暦」になる（Windows）
**症状:** `Get-Date -Format "yyyy"` が令和年(例: 08)になる。
**原因:** ロケールが和暦。
**解決方法:** 西暦で取得する:
`(Get-Date).ToString("yyyy-MM-dd_HHmm", [System.Globalization.CultureInfo]::InvariantCulture)`

---

### 問題: 長尺を細かく抽出して枚数が爆発／重い
**症状:** フレームが数百枚、確認が大変。
**解決方法:** まず粗く(`fps=1/10`)概観 → 必要区間だけ `-ss/-to` で細かく抽出。

---

*最終更新: 2026-06-03*
