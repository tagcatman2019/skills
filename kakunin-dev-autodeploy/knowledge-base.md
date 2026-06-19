# KAKUNIN-DEV オートデプロイスキル - ナレッジベース

このファイルは、実践で得た情報・Tips・コマンド集を蓄積します。

---

## 📝 実践例

### 案件: 静的サイト（Vite 構成） / 2026-06

**体制：**
- 静的サイト（Vite 構成：EJS / SCSS / Tailwind / TypeScript）
- コーディングは外部ベンダー、当方（depart）は**レビュー・フィードバック役**。クライアント環境には触れない（ファイル納品）。

**構成（確定値）：**
- リポジトリ：`depart-develop/{案件名}`（GitHub private）
- 連携ブランチ：`main`
- ビルド：**A案（ビルドあり）**。ビルドコマンド `npm run build` / 出力フォルダ `dist` / ソースはリポジトリ直下
- kakunin：サブドメイン `{案件名}` → `https://{案件名}.kakunin.dev`（Basic 認証あり）
- 接続パス：`prod-depart-kakunin/{案件名}`

**ポイント：**
1. kakunin のボタン式 Git連携は**廃止済み**だったため、GitHub Actions 方式を採用（吉田氏 Notion 準拠）。
2. Secrets は 4 件（`AWS_S3_BUCKET` / `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / `SITE_URL`）。
3. ベンダー担当者（GitHub アカウント）を **Write** 権限でリポジトリに招待。
4. `dist` は `.gitignore` 済みでOK（ソースをコミット→CI でビルドする方式のため正しい状態）。

**成果：**
- 招待 ＋ Secrets 登録 ＋ `.github/workflows/deploy.yml` 設置まで完了。初回実デプロイはベンダーの push 後。

**所要：** ヒアリング含め短時間（手動操作は招待・Secrets・コミットの3点のみ）。

---

## 💡 Tips集

### gh CLI コマンド集

```bash
# 認証状態の確認（自動化可否の判定に使う）
gh auth status

# コラボレーター招待（GitHub ユーザー名が分かる場合・Write 権限）
gh api -X PUT "repos/{org}/{repo}/collaborators/{username}" -f permission=push

# Secrets 登録（非機密＝コマンドに値を書いてよいもの）
gh secret set AWS_S3_BUCKET --repo {org}/{repo} --body "prod-depart-kakunin/{サブドメイン}"
gh secret set SITE_URL      --repo {org}/{repo} --body "https://{サブドメイン}.kakunin.dev"

# Secrets 登録（機密＝シェル履歴に残さない。自分のターミナルで stdin 経由）
gh secret set AWS_ACCESS_KEY_ID     --repo {org}/{repo}
gh secret set AWS_SECRET_ACCESS_KEY --repo {org}/{repo}

# 登録済み Secrets の一覧確認
gh secret list --repo {org}/{repo}
```

### 判断の早見表

- **kakunin 情報はどこ？** → kakunin の各サイト「詳細画面」上部に S3 接続情報（接続パス・アクセスキー ID・シークレットキー）。サブドメインも同画面。
- **SITE_URL の作り方** → `https://{サブドメイン}.kakunin.dev`（末尾スラッシュを取る）。
- **A案 / B案 の決め方** → リポジトリに「ソースを入れて CI でビルド」なら A、「ビルド済みを入れる」なら B。
- **同期元** → A:`./出力フォルダ`、B直下:`.`、Bサブフォルダ:`./フォルダ名`。

### セキュリティ

- AWS の鍵は GitHub Secrets（暗号化）に入れる。コードや YAML に直書きしない（YAML は `${{ secrets.* }}` 参照）。
- 鍵をチャットや共有ツールに流したら、露出範囲を最小化し、長期的にはローテーションを検討。

---

*最終更新: 2026-06-19*
