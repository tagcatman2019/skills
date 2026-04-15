# Coding Rules Skill

## Overview
プロジェクトごとのコーディングルールを段階的に構築し、**ユーザーの指示に基づいてナレッジを蓄積**するスキル。
各プロジェクト開始時に、このテンプレートをプロジェクトディレクトリにコピーして使用します。

## Skill Activation
このスキルは以下の場合に発動します：
- ユーザーが明示的にコーディングルールの確認を依頼した場合
- ユーザーがナレッジの読み込み・登録を依頼した場合
- 新規ページ作成前にユーザーがルール確認を依頼した場合

**重要:** スキル発動時に**ナレッジを自動読み込みしない**。ユーザーの依頼があった時のみ読み込む。

---

## 📁 ナレッジ管理システム

### ナレッジファイル構造

```
{PJディレクトリ}/coding-rules/
├── SKILL.md                    # このスキル本体
├── knowledge/                  # 蓄積されたナレッジ（手動読み込み）
│   ├── environment.md         # 開発環境ナレッジ
│   ├── scss-rules.md          # SCSSルールナレッジ
│   ├── js-rules.md            # JavaScriptルールナレッジ
│   └── ejs-rules.md           # EJSルールナレッジ
└── templates/                  # 質問テンプレート（オプション）
    └── questions.md
```

### ナレッジの4分類

#### 1. 環境ナレッジ (environment.md)
**記録する内容:**
- gulp設定、タスク構成
- ビルドツールの設定
- ファイル監視ルール
- 画像最適化設定
- ブラウザ自動リロード設定
- プロジェクト全体の構成

#### 2. SCSSルール (scss-rules.md)
**記録する内容:**
- ディレクトリ構造
- ファイル分割ルール
- ネーミング規則（BEM等）
- 変数管理（カラー、サイズ、スペーシング）
- mixin/関数の定義
- メディアクエリの書き方
- フォントサイズ単位のルール
- デザインシステム（Figma変数含む）
- ブレイクポイント定義

#### 3. JSルール (js-rules.md)
**記録する内容:**
- ディレクトリ構造
- モジュール分割ルール
- ネーミング規則
- 使用ライブラリ（GSAP, Three.js, Swiper等）
- イベント管理
- 初期化処理
- ユーティリティ関数
- WebGL関連の設定

#### 4. EJSルール (ejs-rules.md)
**記録する内容:**
- テンプレート構造
- パーシャルの分割ルール
- コンポーネント管理
- データの受け渡し
- ネーミング規則
- ファイル配置ルール
- インクルード構造

---

## 🔄 ナレッジ管理のフロー

### 1. ナレッジの読み込み

**ユーザーの依頼例:**
- 「環境ナレッジを読んで」
- 「SCSSルールを確認して」
- 「JSルールとEJSルールを読み込んで」

**Claudeの動作:**
```javascript
// ユーザーの依頼を受けてから読み込み
filesystem:read_text_file を使用して該当ナレッジを読み込み
```

**読み込み後の報告形式:**
```
✅ [ナレッジ名] を読み込みました

【把握した内容】
- 項目1: 概要
- 項目2: 概要
- 項目3: 概要

このナレッジを元に作業を進めます。
```

### 2. ナレッジへの登録

**登録の基本ルール:**
1. **ユーザーが登録を指示した時のみ登録**
2. **登録判断に迷った場合は確認を取る**
3. **登録時は必ず報告する**

**ユーザーの指示例:**
- 「この情報をナレッジに登録して」
- 「SCSSルールに追加して」
- 「環境ナレッジに記録しておいて」

**Claudeの確認パターン:**
```
【ナレッジ登録の確認】

以下の情報を得ました：
[得られた情報の概要]

この情報をナレッジに登録しなくていいですか？
登録する場合、どのナレッジに登録すればよいでしょうか？
- environment.md (開発環境)
- scss-rules.md (SCSSルール)
- js-rules.md (JavaScriptルール)
- ejs-rules.md (EJSルール)
```

**登録後の報告形式:**
```
✅ ナレッジに登録しました

【登録先】
- knowledge/scss-rules.md

【登録内容】
- フォントサイズ単位: PC=rem, SP=vw
- ブレイクポイント: PC=769px以上, SP=480px以下

[更新日時: 2025-10-30 15:30]
```

### 3. ナレッジの更新

**更新が必要な場合:**
- 既存ルールの変更
- 追加情報の補完
- 誤った情報の修正

**更新時の報告形式:**
```
✅ ナレッジを更新しました

【更新先】
- knowledge/js-rules.md

【更新内容】
- GSAPのバージョンを 3.12.2 → 3.12.5 に更新
- ScrollTriggerの初期化方法を追記

[更新日時: 2025-10-30 15:35]
```

---

## 📖 各ナレッジファイルの記録形式

### environment.md の記録形式

```markdown
# 開発環境ナレッジ

## プロジェクト構成
- ビルドツール: gulp
- テンプレートエンジン: EJS
- CSSプリプロセッサ: SCSS
- JavaScriptトランスパイラ: Babel (or なし)

## Gulpタスク設定

### Watchタスク
- 対象: src/ 以下の全ファイル
- 除外: node_modules/, dist/
- ファイル変更時に自動コンパイル

### ビルドタスク
- 本番用: gulp build
- 開発用: gulp dev
- 画像最適化: gulp imagemin

## ディレクトリ構造
```
project/
├── src/
│   ├── ejs/
│   ├── scss/
│   ├── js/
│   └── assets/
└── dist/
```

[作成日: 2025-10-30]
[最終更新: 2025-10-30]
```

### scss-rules.md の記録形式

```markdown
# SCSSルールナレッジ

## ディレクトリ構造

```
scss/
├── foundation/
│   ├── _variables.scss
│   ├── _reset.scss
│   └── _base.scss
├── layout/
│   ├── _header.scss
│   ├── _footer.scss
│   └── _sidebar.scss
└── components/
    ├── _button.scss
    └── _card.scss
```

## ネーミング規則

### BEM記法を使用
- Block: `.btn`
- Element: `.btn__icon`
- Modifier: `.btn--primary`

## フォントサイズ単位ルール

### PC版
- 単位: rem
- 基準: html { font-size: 16px }

### SP版
- 単位: vw
- 変換関数:
```scss
@function sp($px) {
  @return calc($px / 375 * 100vw);
}
```

## ブレイクポイント
- PC: 769px以上
- Tablet: 481px 〜 768px
- SP: 480px以下

## カラー変数
```scss
$color-primary: #0066CC;
$color-secondary: #FF6B6B;
$color-text: #333333;
$color-bg: #FFFFFF;
```

[作成日: 2025-10-30]
[最終更新: 2025-10-30]
```

### js-rules.md の記録形式

```markdown
# JavaScriptルールナレッジ

## ディレクトリ構造

```
js/
├── modules/
│   ├── header.js
│   ├── modal.js
│   └── slider.js
├── utils/
│   ├── scroll.js
│   └── viewport.js
└── main.js
```

## モジュール分割ルール
- ヘッダー固定 → modules/header.js
- モーダル制御 → modules/modal.js
- 共通ユーティリティ → utils/

## 使用ライブラリ

### GSAP 3.12.2
- 用途: アニメーション
- 初期化: js/lib/gsap-init.js
- CDN or npm: npm

### Three.js r150
- 用途: WebGL 3Dビジュアル
- 初期化: js/modules/webgl.js

### Swiper 10.0.0
- 用途: スライダー/カルーセル
- 設定: js/modules/slider.js

## イベント管理
- DOMContentLoaded で初期化
- resize イベントは throttle 処理
- scroll イベントは debounce 処理

## コーディング規約
- const/let を使用（var禁止）
- アロー関数を優先
- セミコロンあり

[作成日: 2025-10-30]
[最終更新: 2025-10-30]
```

### ejs-rules.md の記録形式

```markdown
# EJSルールナレッジ

## ディレクトリ構造

```
ejs/
├── _partials/
│   ├── _header.ejs
│   ├── _footer.ejs
│   └── _meta.ejs
├── _components/
│   ├── _button.ejs
│   ├── _card.ejs
│   └── _modal.ejs
└── pages/
    ├── index.ejs
    └── about.ejs
```

## ファイル命名規則
- パーシャル: `_` プレフィックス（例: `_header.ejs`）
- ページ: プレフィックスなし（例: `index.ejs`）
- コンポーネント: `_` プレフィックス（例: `_button.ejs`）

## インクルード構造
```ejs
<!DOCTYPE html>
<html>
<head>
  <%- include('_partials/_meta') %>
</head>
<body>
  <%- include('_partials/_header') %>
  
  <main>
    <!-- ページコンテンツ -->
  </main>
  
  <%- include('_partials/_footer') %>
</body>
</html>
```

## データの受け渡し
```ejs
<%- include('_components/_button', {
  text: '送信する',
  type: 'primary',
  size: 'large'
}) %>
```

## コンポーネントの定義例
```ejs
<!-- _components/_button.ejs -->
<button class="btn btn--<%= type %> btn--<%= size %>">
  <%= text %>
</button>
```

[作成日: 2025-10-30]
[最終更新: 2025-10-30]
```

---

## 🎯 実践例: ナレッジ管理の流れ

### シナリオ1: 新規プロジェクト開始

```
User: 新しいプロジェクトでgulp + EJS + SCSSを使います

Claude: 
この情報をナレッジに登録しなくていいですか？
environment.md (開発環境ナレッジ) に記録するのが適切かと思います。

User: 登録してください

Claude:
✅ ナレッジに登録しました
【登録先】knowledge/environment.md
【登録内容】
- ビルドツール: gulp
- テンプレートエンジン: EJS  
- CSSプリプロセッサ: SCSS
```

### シナリオ2: SCSSルールの確認

```
User: SCSSルールを読んでください

Claude:
✅ scss-rules.md を読み込みました
【把握した内容】
- ディレクトリ構造: foundation/layout/components
- ネーミング: BEM記法
- フォントサイズ: PC=rem, SP=vw
- ブレイクポイント: PC=769px以上

このルールに従ってSCSSを記述します。
```

### シナリオ3: 新しい情報の追加

```
User: GSAPのバージョンは3.12.5を使います

Claude:
【ナレッジ登録の確認】
以下の情報を得ました:
- GSAP 3.12.5

この情報をjs-rules.mdに登録しなくていいですか？

User: 登録して

Claude:
✅ ナレッジに登録しました
【登録先】knowledge/js-rules.md
【登録内容】
- GSAP 3.12.5を使用ライブラリに追加
```

---

## ⚠️ 重要な注意事項

### 1. ナレッジの自動読み込みは行わない

**❌ 禁止事項:**
- スキル発動時の自動読み込み
- 勝手にナレッジを参照する
- ユーザーの依頼なしでファイルを開く

**✅ 正しい動作:**
- ユーザーが「〇〇ナレッジを読んで」と依頼した時のみ読み込む
- 読み込んだ内容を簡潔に報告
- ナレッジに基づいて作業

### 2. ナレッジ登録は必ず確認する

**確認が必要な状況:**
- 新しい技術情報を得た時
- プロジェクトのルールが明確になった時
- コンポーネントを作成した時
- ライブラリの設定が決まった時

**確認フレーズ:**
「この情報をナレッジに登録しなくていいですか？」

### 3. 登録時は必ず報告する

**報告に含める情報:**
- 登録先のナレッジファイル名
- 登録した内容の概要
- 更新日時

### 4. ファイル操作ツールの使用

**必ず使用:**
- ✅ `filesystem:write_file` - ファイル作成・上書き
- ✅ `filesystem:read_text_file` - ファイル読み込み
- ✅ `str_replace` - 部分編集

**使用禁止:**
- ❌ `create_file` - 使用不可
- ❌ `bash_tool` でのファイル作成

---

## 🔄 project-progress との連携

### 役割分担

**coding-rules/knowledge/ の役割:**
- ✅ 開発環境の詳細設定
- ✅ コーディングルール
- ✅ ネーミング規則
- ✅ 技術的な判断理由

**project-progress/ の役割:**
- ✅ 作業ログ（時系列）
- ✅ 実装した内容の概要
- ✅ 次のタスク

**連携の仕方:**
```
progress/handover.md:
「ヘッダーコンポーネント実装完了
詳細: coding-rules/knowledge/ejs-rules.md 参照」

→ 必要な時だけ coding-rules/knowledge/ を読み込む
```

---

## Progressive Disclosure の実践

### 段階的な情報開示

1. **まず概要を把握**
   - ユーザーの依頼内容を理解
   - 必要なナレッジを特定

2. **必要なナレッジのみ読み込み**
   - 全部読まない
   - 作業に必要な部分だけ

3. **理解度を報告**
   - 把握した内容を簡潔に報告
   - 不明点があれば質問

4. **作業を実行**
   - ナレッジに基づいて作業
   - 新しい情報は登録確認

---

## Integration with Other Skills

このスキルは以下のスキルと連携します：

- **project-progress**: 作業ログは簡潔に、詳細はknowledge/に委譲
- **figma MCP**: 取得した変数情報をscss-rules.mdに記録
- **accessibility-mcp**: アクセシビリティルールをscss-rules.mdやejs-rules.mdに記録
- **theme-factory**: テーマ情報をscss-rules.mdに記録

---

## Version
Version: 3.0.0
Created: 2025-10-28
Last Updated: 2025-10-30
Changes: 
- ナレッジ自動読み込み廃止
- 4分類ナレッジシステム導入（environment/scss/js/ejs）
- 手動読み込み・登録方式に変更
- トークン消費を大幅削減
