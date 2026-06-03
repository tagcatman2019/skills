# Video Frame Review - 使い方ガイド

ローカルmp4やYouTube動画を「フレーム静止画」に変換し、Claudeが視覚的にレビューできるようにするスキルです。

---

## 🚀 クイックスタート

1. このスキルを発動（`SKILL.md` を読む）
2. Claudeが **STEP1: 環境チェック**（ffmpeg / yt-dlp / python）
3. 不足があれば **STEP2: 導入**（確認の上）
4. **STEP3: 対象を聞かれる** → mp4パス or 動画URLを渡す（任意で区間・間隔）
5. **STEP4: 抽出＆講評** → Claudeがフレームを見て報告

---

## 📝 入力の渡し方

- ローカル: `D:\path\to\video.mp4`
- URL: `https://www.youtube.com/watch?v=XXXX`
- 区間（任意）: 「0:15〜0:18」
- 間隔（任意）: 「0.25秒刻み」「全体を粗く」など

---

## 💡 こんな時に使う

- 生成AI動画（Higgsfield/Seedance等）の出来をコマ送りで確認したい
- 参照MVの「文字の出方・カメラ・テンポ」を研究したい
- mp4のある瞬間の構図/破綻を細かく見たい

---

## ⚠️ よくある質問

**Q. Claudeは動画を直接再生できる？**
A. いいえ。標準では画像とPDFのみ。本スキルは「動画→静止画フレーム」に変換して見る運用です。音声は判断できません。

**Q. ffmpegもyt-dlpも入っていない環境でも使える？**
A. 発動時にClaudeが有無を確認し、無ければ導入（winget / pip）を案内します。ローカルmp4だけならffmpegのみでOK。

**Q. 他人のYouTube動画を落としていい？**
A. 私的なフレーム研究用に留め、再配布はしないでください。

---

## 📖 関連

- `SKILL.md` - 本体（4ステップフロー）
- `knowledge-base.md` - Tips・実践例
- `troubleshooting.md` - 既知の問題と解決
- 連携: `video-creation-workflow`（生成動画のレビュー＝STEP9 / 参照クリップのフレーム化）
