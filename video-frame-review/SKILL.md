---
name: "Video Frame Review"
description: "ローカルmp4やYouTube動画を、ffmpeg/yt-dlpでフレーム静止画に変換し、Claudeが画像として視覚レビューできるようにするスキル。環境チェック→不足ツール導入→対象(mp4/URL)のヒアリング→フレーム抽出と講評、の4ステップで標準化"
version: "1.0.0"
---

# Video Frame Review

このスキルは **ローカルmp4やYouTube動画を「フレーム静止画」に変換し、Claudeが視覚的にレビューできるようにする** スキルです。

Claude（Code）は標準で画像(PNG/JPG)とPDFは読めますが、**動画(mp4)の直接再生・音声解析はできません**。本スキルは「動画 → 静止画フレーム」に変換し、標準の画像読み込みで**コマ送り確認**する運用を標準化します。

⚠️ 一度発動したら、対象の動画レビューが終わるまで本フロー（4ステップ）に従う。

---

## 🎯 このスキルの目的

1. **ローカルmp4の内容をフレーム単位で確認・講評する**（生成動画のチェック等）
2. **YouTube等の参照動画をDLしてフレーム確認する**（演出・カメラ・テンポの研究）
3. **環境が無いエンジニアでも、発動すれば環境構築から実施できる**ようにする

---

## 🔧 前提ツール

| ツール | 用途 | チェック | 導入(Windows例) |
|---|---|---|---|
| **ffmpeg** | 動画→フレーム抽出 | `ffmpeg -version` | `winget install Gyan.FFmpeg` |
| **yt-dlp** | YouTube等の取得 | `python -m yt_dlp --version` | `python -m pip install -U yt-dlp` |
| **python + pip** | yt-dlp実行基盤 | `python --version` / `python -m pip --version` | python.org / `winget install Python.Python.3.12` |

※ Claudeが画像を読むのは**標準機能**（追加不要）。
※ **ローカルmp4だけ**を見たい場合は **ffmpegのみ**でOK（yt-dlp/pythonは不要）。

---

## 🚀 実行フロー（4ステップ）

### STEP 1: 環境が整っているか確認

最初に必ず、必要ツールの有無をチェックする（Bashツール）。

```bash
command -v ffmpeg >/dev/null && echo "ffmpeg OK" || echo "NO ffmpeg"
python -m yt_dlp --version >/dev/null 2>&1 && echo "yt-dlp OK" || echo "NO yt-dlp"
python --version >/dev/null 2>&1 && echo "python OK" || echo "NO python"
```

- 必要なものが揃っていれば → **STEP 3** へ
- 不足があれば → **STEP 2** へ
- ※ローカルmp4のみ希望なら、ffmpegが有ればOK（yt-dlp/python不足は無視可）

---

### STEP 2: 環境を作る（不足ツールの導入）

①で不足していたものだけ、**ユーザーに確認の上**で導入する。

```bash
# ffmpeg（Windows / winget）
winget install Gyan.FFmpeg
#   ↑不可なら公式 https://ffmpeg.org/download.html をDLしてPATHを通す

# yt-dlp（pythonが必要）
python -m pip install -U yt-dlp

# python が無い場合
winget install Python.Python.3.12
#   ↑または python.org からインストール
```

導入後、**STEP1のチェックを再実行**して整ったことを確認 → STEP 3。

> 導入はシステム変更なので、勝手に進めず「入れてよいか」を一言確認してから実行する。

---

### STEP 3: 対象をヒアリング

環境が整っていたら、ユーザーに尋ねる：

```
【動画レビュー対象を教えてください】

① ローカルのmp4パス（例: D:\path\to\xxx.mp4）
   または
② 動画URL（例: https://www.youtube.com/watch?v=XXXX）

【任意】
- 見たい区間（例: 0:15〜0:18）。無ければ全体を粗く確認
- 抽出間隔の希望：
  - 速い演出/細かく見たい → 0.25秒刻み(fps=4) 〜 0.125秒(fps=8)
  - ふつう → 0.5〜1秒刻み(fps=2〜1)
  - 全体概観（長尺） → 5〜10秒刻み(fps=1/5〜1/10)
```

> 第三者の動画URLは「私的なフレーム研究用」に留める旨を念頭に。

---

### STEP 4: 揃ったら確認（抽出 → 閲覧 → 講評）

入力が揃ったら抽出し、Claudeがフレームを `Read` で開いて講評する。

**(A) ローカルmp4の場合**

```bash
# 尺・解像度の確認
ffmpeg -i "INPUT.mp4" 2>&1 | grep -E "Duration|Stream #0:0"

mkdir -p frames_out

# 全体を等間隔抽出（例: 0.5秒ごと=2fps、横640に縮小）
ffmpeg -y -loglevel error -i "INPUT.mp4" -vf "fps=2,scale=640:-1" frames_out/f_%03d.png

# 区間だけ細かく（例: 15〜18秒を0.25秒刻み=4fps、横720）
ffmpeg -y -loglevel error -ss 15 -to 18 -i "INPUT.mp4" -vf "fps=4,scale=720:-1" frames_out/s_%02d.png
```

**(B) YouTube等のURLの場合**

```bash
# 720p以下で取得（フレーム確認には十分・軽い）
python -m yt_dlp -f "bv*[height<=720]+ba/b[height<=720]" --merge-output-format mp4 -o "ref_%(id)s.%(ext)s" "URL"
# 取得後は (A) と同じく ffmpeg でフレーム抽出
```

**(任意) コンタクトシート**（連番を1枚に並べて全体把握。GPT等に渡す時にも便利）

```bash
ffmpeg -framerate 1 -i frames_out/f_%03d.png -vf "scale=360:-1,tile=5x3:padding=4:color=black" contact_sheet.png
```

**講評**：Claude が代表フレーム（連番PNG / コンタクトシート）を `Read` で開き、以下を評価して報告する：
- 構図・カメラワーク・ライティング・色
- モーションの進行（コマ間の変化）
- テキストや要素の保持/崩れ・分割などの破綻
- 問題点と次の改善案

---

## 💡 実践例

```
【ユーザー】
video-frame-review を発動。D:\proj\out\test.mp4 の 0:15〜0:18 を細かく見て。

【Claude】
STEP1: 環境チェック → ffmpeg OK（yt-dlpは今回不要）
STEP3: 区間 0:15〜0:18・0.25秒刻みで了解
STEP4:
  ffmpeg -ss 15 -to 18 -i "...test.mp4" -vf "fps=4,scale=720:-1" frames_out/s_%02d.png
  → s_01〜s_13 を Read で確認
  講評: 「2分割が焼き付き／文字はブロック単位でポップ／カメラは固定」など
```

---

## ⚠️ 注意事項

- **静止画ベースの近似**。動きの滑らかさ・タイミングの微妙さ・**音声は判断不可**（必要なら抽出間隔を細かく）
- **第三者の動画は私的なフレーム研究用**に留め、再配布しない
- 抽出フレームは作業フォルダ（例 `frames_out/`）にまとめ、不要になったら掃除する
- 長尺をいきなり細かく抽出すると枚数が爆発する。まず粗く（5〜10秒刻み）概観 → 必要区間を細かく、の二段構えが効率的
- Claudeが一度に読む枚数は欲張らず、代表フレームを間引いて読むとトークン効率が良い

---

## 🔄 他のスキルとの連携

### claude-fundamental-rules
- 基本ルール（編集許可制・ハルシネーション防止）を前提とする。**ツール導入(STEP2)は確認の上で実行**。

### video-creation-workflow
- **STEP 9（結果レビュー）** の実作業を本スキルが担う（生成mp4のコマ送り講評）
- **「参照クリップから高再現度の絵コンテ」** のフレーム抽出・コンタクトシート作成にも使う

---

## 📖 関連ドキュメント

- `knowledge-base.md` - 実践で得たTips・ベストプラクティス
- `troubleshooting.md` - 既知の問題と解決方法
- `README.md` - 使い方ガイド

---

*このスキルで、動画を「フレーム」で確実にレビューしましょう。*
