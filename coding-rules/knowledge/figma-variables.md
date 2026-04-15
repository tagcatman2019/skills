# Figmaバリアブルルール

## バリアブルファイルの場所

```
src/scss/_dev/global/_figma-variables.scss
```

---

## 🎯 このナレッジを読むタイミング

- Figma IDからコンポーネントをコーディングする時
- デザインカンプを見ながらコーディングする時
- 色・スペーシング・タイポグラフィを実装する時

---

## ⚠️ バリアブル使用の必須ルール

### 1. 色は必ずFigmaバリアブルを使用

```scss
// ❌ 禁止
color: #ce0037;
background-color: #f3f3f3;

// ✅ 正解
color: $figma-color-primary;
background-color: $figma-color-gray-5;
```

### 2. スペーシングはFigmaバリアブルを優先

```scss
// ✅ 推奨（PC）
margin-bottom: $figma-space-60-pc;  // 3.75rem
padding: $figma-space-40-pc $figma-space-20-pc;  // 2.5rem 1.25rem

// ✅ 推奨（SP）
@media (max-width: 768px) {
  margin-bottom: sp(60);  // 15.38vw
  padding: sp(40) sp(20);  // 10.26vw 5.13vw
}

// ⚠️ Figmaに定義がない値のみ、個別指定OK
padding-left: rem(18);  // PC: 1.125rem

@media (max-width: 768px) {
  padding-left: sp(18);  // SP: 4.62vw
}
```

### 3. タイポグラフィはmixinを使用

```scss
// ❌ 禁止
.title {
  font-size: rem(36);
  font-weight: 700;
  line-height: 1.8;
  font-family: "Noto Sans JP";
  
  @media (max-width: 768px) {
    font-size: sp(36);
  }
}

// ✅ 正解
.title {
  @include figma-ttl-1;  // PC/SPのレスポンシブ対応も自動
}
```

---

## 📐 レスポンシブ単位のルール

### 基本方針
- **PC**: `rem` 単位（基準: 16px）
- **SP**: `vw` 単位（基準: 390px、sp()関数使用）
- **ブレークポイント**: 768px

### 変換関数

#### PC用: rem()関数
```scss
@function rem($px) {
  @return ($px / 16) * 1rem;
}

// 使用例
.element {
  font-size: rem(20);  // 1.25rem
  padding: rem(40);    // 2.5rem
}
```

#### SP用: sp()関数
```scss
@function sp($px) {
  @return ($px / 390) * 100vw;
}

// 使用例
@media (max-width: 768px) {
  .element {
    font-size: sp(20);  // 5.13vw
    padding: sp(40);    // 10.26vw
  }
}
```

---

## 📚 利用可能なバリアブル一覧

### カラー

#### ベースカラー
- `$figma-color-black-100`: #000000（完全な黒）
- `$figma-color-black`: #191919（テキスト用黒）
- `$figma-color-white`: #ffffff
- `$figma-color-primary`: プライマリカラー（アクセント）

#### ブランドカラー
- `$figma-color-brand-1`: ブランドカラー1
- `$figma-color-brand-2`: ブランドカラー2

#### グレースケール
- `$figma-color-gray-3`: #f8f8f8（最も薄い）
- `$figma-color-gray-5`: #f3f3f3
- `$figma-color-gray-10`: #e5e5e5
- `$figma-color-gray-15`: #d9d9d9
- `$figma-color-gray-35`: #a7a7a7（最も濃い）

#### ライトカラー（透明度付き）
- `$figma-color-light-50`: rgba(200, 207, 215, 0.5)
- `$figma-color-light-95`: rgba(200, 207, 215, 0.95)

---

### スペーシング（PC用）

```scss
$figma-space-4-pc    // 0.25rem (4px)
$figma-space-8-pc    // 0.5rem (8px)
$figma-space-12-pc   // 0.75rem (12px)
$figma-space-16-pc   // 1rem (16px)
$figma-space-20-pc   // 1.25rem (20px)
$figma-space-24-pc   // 1.5rem (24px)
$figma-space-28-pc   // 1.75rem (28px)
$figma-space-32-pc   // 2rem (32px)
$figma-space-36-pc   // 2.25rem (36px)
$figma-space-40-pc   // 2.5rem (40px)
$figma-space-60-pc   // 3.75rem (60px)
$figma-space-80-pc   // 5rem (80px)
$figma-space-100-pc  // 6.25rem (100px)
$figma-space-120-pc  // 7.5rem (120px)
$figma-space-160-pc  // 10rem (160px)
$figma-space-200-pc  // 12.5rem (200px)
```

### スペーシング（SP用）

```scss
// sp()関数を使用
sp(4)    // 1.03vw
sp(8)    // 2.05vw
sp(12)   // 3.08vw
sp(16)   // 4.10vw
sp(20)   // 5.13vw
sp(24)   // 6.15vw
sp(28)   // 7.18vw
sp(32)   // 8.21vw
sp(36)   // 9.23vw
sp(40)   // 10.26vw
sp(60)   // 15.38vw
sp(80)   // 20.51vw
sp(100)  // 25.64vw
sp(120)  // 30.77vw
sp(160)  // 41.03vw
sp(200)  // 51.28vw
```

---

### タイポグラフィ（mixin）

#### 汎用タイトル
- `@include figma-ttl-1;` // 36px / Bold / lh:1.8
- `@include figma-ttl-2;` // 28px / Bold / lh:1.8
- `@include figma-ttl-3;` // 20px / Bold / lh:1.8
- `@include figma-ttl-4;` // 16px / Bold / lh:2.0
- `@include figma-ttl-5;` // 14px / Bold / lh:2.0

#### TOPページ用タイトル（日本語）
- `@include figma-top-t1-jp;` // 50px / Bold / lh:1.6
- `@include figma-top-t2-jp;` // 36px / Bold / lh:1.6
- `@include figma-top-t3-jp;` // 28px / Bold / lh:1.6
- `@include figma-top-t4-jp;` // 24px / Bold / lh:1.6

#### TOPページ用タイトル（英語・Bebas Neue）
- `@include figma-top-t1-en;` // 120px / Regular / lh:1.0
- `@include figma-top-t2-en;` // 80px / Regular / lh:1.0
- `@include figma-top-t3-en;` // 40px / Regular / lh:1.0
- `@include figma-top-t4-en;` // 24px / Regular / lh:1.0
- `@include figma-top-t5-en;` // 20px / Regular / lh:1.0

#### 本文・キャプション
- `@include figma-body;`    // 16px / Regular / lh:2.0
- `@include figma-body-s;`  // 14px / Regular / lh:1.6
- `@include figma-caption;` // 12px / Regular / lh:1.8
- `@include figma-count;`   // 10px / DemiLight / lh:1.8

---

## 💡 使用例

### スペーシングの書き方

```scss
.section {
  // PC: rem単位
  padding: $figma-space-80-pc 0;  // 5rem 0
  margin-bottom: $figma-space-60-pc;  // 3.75rem
  
  // SP: vw単位（関数使用）
  @media (max-width: 768px) {
    padding: sp(80) 0;  // 20.51vw 0
    margin-bottom: sp(60);  // 15.38vw
  }
}
```

### タイポグラフィの書き方（mixin使用）

```scss
.title {
  @include figma-ttl-1;
  color: $figma-color-primary;
  // ↓ 自動的に以下が適用される
  // PC: font-size: 2.25rem (36px)
  // SP: font-size: 9.23vw (36px相当)
  // font-weight: 700
  // line-height: 1.8
}
```

### カスタム値の書き方

```scss
.custom-element {
  // Figmaバリアブルにない値
  font-size: rem(18);  // PC: 1.125rem
  
  @media (max-width: 768px) {
    font-size: sp(18);  // SP: 4.62vw
  }
}
```

### 色の組み合わせ

```scss
.button {
  @include figma-ttl-4;
  color: $figma-color-white;
  background-color: $figma-color-primary;
  padding: $figma-space-16-pc $figma-space-32-pc;
  
  @media (max-width: 768px) {
    padding: sp(16) sp(32);
  }
  
  &:hover {
    background-color: $figma-color-brand-1;
  }
}
```

---

## 🔄 Figma IDからのコーディングフロー

### 1. Figmaからデザイン情報を取得
```
figma:get_design_context でコンポーネント情報を取得
```

### 2. 色・スペーシング・フォントの照合
```
Figmaで使われている値 → Figmaバリアブルで再現
```

### 3. カスタム値の扱い
```
Figmaバリアブルに存在しない値 → rem()/sp()関数で記述
近い値があれば変換を提案
```

---

## 💡 実例：Figmaから取得した情報の変換

### 取得したFigma情報
```
color: #ce0037
padding: 40px 20px
font: 36px Bold, line-height: 1.8, Noto Sans JP
```

### SCSSへの変換
```scss
.component {
  @include figma-ttl-1;  // フォント情報をmixinで適用（PC/SP自動対応）
  color: $figma-color-primary;  // #ce0037 → 変数化
  padding: $figma-space-40-pc $figma-space-20-pc;  // PC用変数
  
  @media (max-width: 768px) {
    padding: sp(40) sp(20);  // SP用関数
  }
}
```

---

## 🎯 変数選択の判断基準

### 色の選択
1. Figmaで指定されている色コード
2. 最も近いFigmaバリアブル
3. 一致しない場合は、ユーザーに確認

### スペーシングの選択
1. Figmaで指定されている数値
2. Figmaバリアブルに存在するか確認
3. 存在する → PC用変数 + SP用関数
4. 存在しない → rem()/sp()関数で直接記述

### タイポグラフィの選択
1. Figmaのfont-size, weight, line-heightを確認
2. 完全一致するmixinを使用
3. 一致しない場合は、個別指定してユーザーに報告

---

## 📌 よくある質問

**Q: Figmaバリアブルに存在しない値を使いたい**
```scss
A: rem()/sp()関数で直接記述してOK
例: padding: rem(18);  // PC
    padding: sp(18);   // SP
```

**Q: PC/SPで異なる値を使いたい**
```scss
A: メディアクエリ内で別の値を指定OK
例:
.element {
  padding: rem(40);  // PC
  
  @media (max-width: 768px) {
    padding: sp(32);  // SP（別の値）
  }
}
```

**Q: 複数のmixinを組み合わせたい**
```scss
A: 基本的には1つのmixinを使用。カスタマイズが必要な場合は個別指定
例:
.custom-title {
  @include figma-ttl-1;
  letter-spacing: 0.05em;  // 追加のカスタマイズ
}
```

**Q: 関数とmixinの使い分けは？**
```scss
A: 
- タイポグラフィ → mixin（PC/SP自動対応）
- スペーシング → PC用変数 + SP用関数
- カスタム値 → rem()/sp()関数
```

---

## 📝 プロジェクト別カスタマイズ

このドキュメントは汎用的なFigmaバリアブルルールです。
実際のプロジェクトでは、以下の情報を追記してください：

- 具体的な色の値（`$figma-color-primary`など）
- プロジェクト固有のバリアブル名
- Figmaファイル名とNode ID
- ブレークポイントが異なる場合はその値
- SP基準が390px以外の場合はその値
