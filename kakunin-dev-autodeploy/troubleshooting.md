# KAKUNIN-DEV オートデプロイスキル - トラブルシューティング

このファイルは、発生した問題と解決方法を記録します。

---

## 🔧 よくある問題と解決方法

### 問題: kakunin 管理画面の「Git連携」ボタンで連携しても自動デプロイされない

**症状：** kakunin の Git連携フォーム（GitのURL/連携ブランチ/リリースパス）を埋めても反映されない。
**原因：** kakunin のボタン式 Git連携機能は**廃止済み（動かない）**（2026-06 時点・吉田氏談）。
**解決方法：** GitHub Actions 方式（`deploy.yml` で S3 sync）を使う。フォームは埋めても無駄。
**予防策：** 本スキルは最初から GitHub Actions 方式で進める。

---

### 問題: `npm ci` が失敗する（Install ステップで赤）

**症状：** `npm ci can only install with an existing package-lock.json` 等。
**原因：** リポジトリに `package-lock.json` が無い／別のパッケージマネージャ（pnpm・yarn）を使用。
**解決方法：**
1. ロックファイルをコミットしてもらう（推奨）、または
2. `deploy.yml` の `run: npm ci` を `run: npm install` に変更し、`cache: 'npm'` 行も外す。
3. pnpm/yarn の場合は該当のセットアップ・インストールに差し替える。

---

### 問題: ビルドが Node バージョンで失敗する

**症状：** `Unsupported engine` / 構文エラー等。
**原因：** ローカルと CI の Node バージョン不一致。
**解決方法：** `package.json` の `engines.node` に合わせて `deploy.yml` の `node-version` を変更。

---

### 問題: サイトの表示が崩れる／ファイルがフォルダごとアップされる

**症状：** CSS/画像が読めない、`https://.../dist/...` のような階層になっている。
**原因：** S3 への**同期元（sync 元）の指定ミス**。
**解決方法：** 同期元を正す。
- A案（ビルドあり）：`aws s3 sync ./{出力フォルダ}`（例 `./dist`）
- B案・直下：`aws s3 sync .`（`--exclude ".git/*" --exclude ".github/*"`）
- B案・サブフォルダ：`aws s3 sync ./{フォルダ名}`

---

### 問題: `SITE_URL` が原因でビルド時に URL がおかしくなる

**症状：** 生成された絶対 URL の末尾が二重スラッシュ等。
**原因：** `SITE_URL` に末尾スラッシュを含めた。
**解決方法：** `SITE_URL` は**末尾スラッシュなし**（`https://{サブドメイン}.kakunin.dev`）で登録。

---

### 問題: S3 で AccessDenied / NoSuchBucket

**症状：** `S3 sync Deploy` ステップで権限エラーやバケット不明。
**原因：** Secrets の値ミス（接続パス／キー）、または接続パスの綴り違い。
**解決方法：** kakunin 詳細画面の値と Secrets を突き合わせる。`AWS_S3_BUCKET` は `prod-depart-kakunin/{サブドメイン}` の形。`region` は `ap-northeast-1`。

---

### 問題: 招待が「Pending」のまま push できない

**症状：** ベンダーが push できない／Collaborators に "Awaiting response"。
**原因：** 招待は相手の**承認待ち**（正常な状態）。
**解決方法：** 相手に招待メールの承認を依頼。承認後に push 可能になる。

---

### 問題: deploy.yml はあるのに Actions が走らない

**症状：** push しても Actions タブに何も出ない。
**原因：** `on.push.branches` と実際の push 先ブランチが不一致／`deploy.yml` のパスが `.github/workflows/` 配下でない。
**解決方法：** 連携ブランチ（通常 `main`）と `branches: ['main']` を一致させる。ファイルパスを確認。

---

*最終更新: 2026-06-19*
