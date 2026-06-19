---
name: "KAKUNIN-DEV オートデプロイスキル"
description: "KAKUNIN-DEV（depart社内の確認環境）へGitHub Actionsでオートデプロイを設定する手順を半自動化するスキル。情報収集→deploy.yml生成→コラボレーター招待→Secrets登録→workflow設置まで案内。gh CLIが使えれば招待・設置を自動実行し、手動が必要な箇所（kakunin情報取得・Secrets登録）は1ステップずつガイドする。"
version: "1.0.0"
---

# KAKUNIN-DEV オートデプロイスキル

このスキルは **KAKUNIN-DEV（depart 社内の確認環境）への GitHub Actions オートデプロイ設定を半自動で行う** スキルです。
新しい案件で「GitHub にマージ → 確認環境へ自動反映」を組むとき、最初に読み込ませてください。

> **前提知識：**
> KAKUNIN-DEV（kakunin）は depart 社内エンジニアが自作した確認/プレビュー環境（AWS S3 ベース）。ネット上に公開ドキュメントは無く、仕様の一次情報は社内（管理担当：吉田圭吾）。
> ⚠️ **kakunin 管理画面の「Git連携」ボタンによる連携機能は廃止済み（動かない）。** デプロイは **GitHub Actions → S3 sync** で行う（吉田氏 Notion「kakunin.devにGithubActionsからオートデプロイする」準拠）。

---

## 🎯 このスキルの目的

1. KAKUNIN-DEV へのオートデプロイ設定を、毎回迷わず最短で組めるようにする
2. **自動化できる箇所**（deploy.yml 生成・招待・コミット）は AI アシスタントが実行する
3. **手動が必要な箇所**（kakunin 情報の取得・Secrets 登録）は 1 ステップずつ案内する
4. 既知の落とし穴（`troubleshooting.md`）を最初から回避する

---

## 🤖 自動 / ✋ 手動 の切り分け

| ステップ | 区分 | 内容 |
|---|---|---|
| 必要情報のヒアリング・整理 | 🤖 自動 | このスキルが質問し、回答を整理 |
| ビルド有無の判定・`deploy.yml` 生成 | 🤖 自動 | 案件に合わせて雛形を確定生成 |
| Secrets の name↔value 対応表・`gh` コマンド生成 | 🤖 自動 | 貼るだけ／流すだけの形にする |
| コラボレーター招待 | 🤖 自動※ / ✋ 手動 | `gh` 認証済みなら自動。無ければ web UI を案内 |
| `deploy.yml` のコミット | 🤖 自動※ / ✋ 手動 | `gh`/git が使えれば自動。無ければ web UI を案内 |
| **kakunin 詳細画面から S3 情報を取得** | ✋ 手動 | kakunin に API が無いため**必ず手動**（画面からコピー） |
| **Secrets 登録（鍵の値）** | ✋ 手動 | セキュリティ上、**鍵はチャットに貼らず** GitHub UI か自分のターミナルで |
| 初回デプロイ失敗時のログ解析・修正提案 | 🤖 自動 | Actions のログを見て直し方を提示 |

※「自動※」は `gh` CLI が認証済み（`gh auth status` が通る）かつ対象 repo を操作できる場合のみ。条件を満たさなければ手動ガイドにフォールバックする。

---

## 🚀 使い方

このスキル一式（`kakunin-dev-autodeploy/`）を、お使いの AI コーディングアシスタント（Claude Code / GitHub Copilot 等）に読み込ませ、次のように伝えます：

```
KAKUNIN-DEV のオートデプロイをセットアップしたいです。案件名は {案件名} です。
```

読み込み後、下記フローで進行します。

---

## 📝 実行フロー

### ステップ0：前提確認
- 確認環境は **KAKUNIN-DEV（kakunin）** か？（他の環境なら別手順）
- デプロイ方式は **GitHub Actions → S3 sync**（kakunin のボタン連携は使わない）

### ステップ1：必要情報の収集

以下を質問し、揃った情報を整理する（不足分はユーザー or 社内に確認）。

**A. kakunin 側（詳細画面から取得：✋手動）**
- 接続パス：`prod-depart-kakunin/{サブドメイン}`
- AWS アクセスキー ID
- AWS シークレットアクセスキー
- サブドメイン → 公開 URL：`https://{サブドメイン}.kakunin.dev`

**B. GitHub 側**
- リポジトリ：`{org}/{repo}`（例：`depart-develop/{案件名}`）
- 連携ブランチ：通常 `main`
- 招待するアカウント（ベンダー等）：GitHub ユーザー名 or メールアドレス

**C. ビルド構成（ステップ2の判定材料）**
- リポジトリに入れるのは「ビルド前ソース」か「ビルド済みファイル」か
- ビルドありなら：ビルドコマンド（例 `npm run build`）と出力フォルダ（例 `dist`）
- ビルドなしなら：公開ファイルの場所（リポジトリ直下 or サブフォルダ名）

### ステップ2：ビルド有無の判定 → `deploy.yml` 生成（🤖自動）

| 条件 | 採用 | 同期元 |
|---|---|---|
| ビルド前ソースを入れて CI でビルド | **A案（ビルドあり）** | `./{出力フォルダ}`（例 `./dist`） |
| ビルド済みファイルを直接入れる（直下） | **B案（静的）** | `.`（リポジトリ直下） |
| ビルド済みファイルをサブフォルダに入れる | **B案（静的）** | `./{サブフォルダ}` |

→ 下記「📄 deploy.yml 雛形」から該当案を、案件の値で確定生成する。

### ステップ3：コラボレーター招待

- **🤖自動（`gh` 認証済み・GitHub ユーザー名が分かる場合）**
  ```bash
  gh api -X PUT "repos/{org}/{repo}/collaborators/{username}" -f permission=push
  ```
- **✋手動（メールしか無い／`gh` 不可）**：web UI で案内
  1. repo → Settings → Collaborators and teams → Add people
  2. ユーザー名 or メールを入力 → 権限 **Write** → Add to repository
- 招待後は相手が**承認するまで Pending**（正常）。承認しないと push できない。

### ステップ4：Secrets 登録（4件）

| Secret 名 | 値 |
|---|---|
| `AWS_S3_BUCKET` | 接続パス `prod-depart-kakunin/{サブドメイン}` |
| `AWS_ACCESS_KEY_ID` | kakunin の AWS アクセスキー ID |
| `AWS_SECRET_ACCESS_KEY` | kakunin の AWS シークレットアクセスキー |
| `SITE_URL` | 公開 URL `https://{サブドメイン}.kakunin.dev`（**末尾スラッシュなし**） |

- **✋手動（推奨）**：repo → Settings → Secrets and variables → Actions → **New repository secret** で1件ずつ。**鍵の値はここに直接貼る**（チャットや共有ログに残さない）。
- **🤖/自分のターミナル**：`gh` でも可。ただし値がシェル履歴に残るため、**鍵は自分のターミナルで実行**するのが安全。
  ```bash
  gh secret set AWS_S3_BUCKET       --repo {org}/{repo} --body "prod-depart-kakunin/{サブドメイン}"
  gh secret set SITE_URL            --repo {org}/{repo} --body "https://{サブドメイン}.kakunin.dev"
  gh secret set AWS_ACCESS_KEY_ID   --repo {org}/{repo}   # 値はプロンプト/stdinで（履歴に残さない）
  gh secret set AWS_SECRET_ACCESS_KEY --repo {org}/{repo} # 同上
  ```
  ※ B案（ビルドなし）では `SITE_URL` はワークフローで使わないため任意。

### ステップ5：`deploy.yml` 設置

設置先：`.github/workflows/deploy.yml`（`main` ブランチ）

- **🤖自動（repo がローカルにクローン済み）**：ファイルを作成し、git で push。
  - ⚠️ コミットメッセージはシンプルに（例 `Create deploy.yml`）。**AI アシスタントの痕跡（Co-Authored-By 等）は付けない。**
- **✋手動**：repo → Actions →「set up a workflow yourself」→ ファイル名を `deploy.yml` に → 雛形を貼り付け → Commit（`main`）。

### ステップ6：初回デプロイ確認

- ベンダー/利用者が `main` に push/マージ → Actions が起動。
- 成功（緑）なら確認環境に反映。`https://{サブドメイン}.kakunin.dev` で確認（Basic 認証あり）。
- 失敗（赤）なら **Actions タブのログ**を確認 → `troubleshooting.md` 参照、または失敗ログを AI アシスタントに渡して修正。

---

## 📄 deploy.yml 雛形

### A案：ビルドあり（CI で `npm run build` → 出力フォルダを同期）

```yaml
name: deploy CI

on:
  push:
    branches: ['main']        # 連携ブランチに合わせる

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20      # package.json の engines に合わせて調整
          cache: 'npm'

      - name: Install
        run: npm ci

      - name: Build
        env:
          SITE_URL: ${{ secrets.SITE_URL }}
        run: npm run build       # 案件のビルドコマンドに合わせる

      - name: AWS - set Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-northeast-1

      - name: S3 sync Deploy
        run: aws s3 sync ./dist s3://${{ secrets.AWS_S3_BUCKET }} --delete --quiet
        # 出力フォルダが dist 以外なら ./dist を変更
```

### B案：ビルドなし（ビルド済みファイルを直接同期）

```yaml
name: deploy CI

on:
  push:
    branches: ['main']

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: AWS - set Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-northeast-1

      - name: S3 sync Deploy
        run: |
          aws s3 sync . s3://${{ secrets.AWS_S3_BUCKET }} --delete --quiet \
            --exclude ".git/*" --exclude ".github/*"
        # 公開ファイルがサブフォルダなら `aws s3 sync .` を `aws s3 sync ./サブフォルダ` に変更（--exclude は不要）
```

---

## ⚠️ 重要な注意事項

1. **kakunin のボタン式 Git連携は使わない**（廃止済み）。必ず GitHub Actions 方式。
2. **鍵はチャット/共有ログに貼らない**。Secrets 登録は GitHub UI か自分のターミナルで。露出した鍵は早めにローテーションを検討。
3. **コミットに AI アシスタントの痕跡を残さない**（Co-Authored-By 等を付けない）。
4. **同期元の指定ミスに注意**：A案は `./dist`（=出力フォルダ）、B案直下は `.`、サブフォルダは `./フォルダ名`。間違えるとサイトが崩れる。
5. `region` は `ap-northeast-1`（東京）。`SITE_URL` は**末尾スラッシュなし**。
6. `npm ci` は `package-lock.json` 必須（無ければ `npm install`／別パッケージマネージャなら調整）。
7. 仕様で迷ったら **Web 検索ではなく社内（吉田氏）** に確認（kakunin は社内ツール）。

---

## 📖 関連ドキュメント

- `knowledge-base.md` - 実践例・Tips・`gh` コマンド集
- `troubleshooting.md` - 既知の落とし穴と解決方法
- `README.md` - クイック使い方

---

## 🔄 前提・運用ルール

このスキルは以下の運用前提で動きます（AI アシスタント・人どちらが実行する場合も共通）。

- **編集は許可を得てから**：ファイルの作成・変更は、内容を提示して承認を得てから行う。
- **確証のない情報は断定しない**：仕様が不明な点は推測で進めず、社内（kakunin 管理担当）に確認する。
- **記録を残す**：セットアップ完了時は案件の進捗・引き継ぎメモに記録しておくと安全。

> ※ Claude Code で使う場合、上記は `claude-fundamental-rules` / `progress` スキルに対応します（任意）。

---

*KAKUNIN-DEV へのオートデプロイを、毎回サクッと。*
