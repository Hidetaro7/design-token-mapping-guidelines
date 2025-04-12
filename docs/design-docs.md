# Design Token Mapping Guidelines
**Version: 0.2**  
**準拠仕様: W3C Design Tokens Draft / Tailwind CSS v4 Ready / OKLCH-based Color System**

---

## 1. 概要

このドキュメントは、**Figmaで定義された変数トークンとTailwind CSSのユーティリティクラスのマッピングルール**を組織化して、

- デザインと実装の一貫性
- AIによるコード生成の再現性
- コンポーネント再利用の基礎

を実現するための「設計ルールの憲法」です。

---

## 2. 技術準拠と互換性

本ドキュメントは [W3C Design Tokens Community Group](https://www.w3.org/community/design-tokens/) による [Design Tokens Format Module](https://tr.designtokens.org/format/) に準拠した構造を採用しています。

### 採用理由
- Figma / Tokens Studio / Style Dictionary との互換性が高い
- JSON形式による明示的な構造と `$type` 情報の保持
- ChatGPTなどAIに対し構造化された制約情報を提供できる
- Tailwind CSSやその他フレームワークへも広く展開しやすい

### `$type` の意味と活用
各トークンには `$type` を指定し、値の意味的な型を明示します。これによりツールやAIが適切に処理できます。

| `$type`          | 意味・用途                     | 例                           |
|------------------|----------------------------------|------------------------------|
| `color`          | 色を表す                        | `#3B82F6`, `oklch(...)`      |
| `dimension`      | サイズ・距離・余白など          | `1rem`, `0.5em`, `24px`      |
| `borderRadius`   | 角丸（border-radius）            | `0.375rem`                   |
| `typography`     | タイポグラフィ設定（複合情報）   | `text-lg font-bold` など     |
| `fontFamily`     | フォントファミリー指定           | `Inter, sans-serif`         |

例：
```json
"spacing": {
  "4": {
    "$value": "1rem",
    "$type": "dimension"
  }
}
```
この指定により、「1remは距離・余白を意味する」という情報が明確になります。

---

## 2.1 カラー設計方針（OKLCHベース）

- カラーはすべて `oklch(L C H)` 形式で定義し、色相 (`H`) を保ちながら明度 (`L`) と彩度 (`C`) を操作して階調を展開する。
- 階調展開例：
  - `primary/100` → 明度95%（背景色や非アクティブ状態）
  - `primary/500` → 明度60%（通常状態）
  - `primary/700` → 明度40%（hoverやactiveなど）

```json
"color": {
  "primary": {
    "100": { "$value": "oklch(95% 0.02 240)", "$type": "color" },
    "500": { "$value": "oklch(60% 0.15 240)", "$type": "color" },
    "700": { "$value": "oklch(40% 0.15 240)", "$type": "color" }
  },
  "background": {
    "DEFAULT": { "$value": "oklch(98% 0.01 260)", "$type": "color" }
  },
  "text": {
    "DEFAULT": { "$value": "oklch(15% 0.02 260)", "$type": "color" }
  }
}
```

---

## 2.2 タイポグラフィ設計方針

- タイポグラフィは用途ベースで命名（例：`heading-md`, `body-sm`）し、Tailwindのユーティリティクラスを `$value` に記述。
- `typography` 型は `text-*`, `font-*`, `leading-*` などを組み合わせた**見た目の粒度定義**として利用する。

```json
"font": {
  "heading-md": {
    "$value": "text-lg font-bold",
    "$type": "typography"
  },
  "body-sm": {
    "$value": "text-sm font-sans",
    "$type": "typography"
  }
}
```

---

## 3. Figma変数の命名ルール

| Figma Variable        | Tailwind Utility               | 備考                   |
|------------------------|--------------------------------|------------------------|
| `spacing/4`           | `p-4`, `m-4`                   | `1rem`                |
| `color/primary`       | `bg-primary`, `text-primary`   | メインカラー           |
| `radius/md`           | `rounded-md`                   | `0.375rem`             |
| `font/heading-md`     | `text-lg font-bold`            | 中見出し               |
| `font/body-sm`        | `text-sm font-sans`            | 小文本                 |
| `fontFamily/sans`     | `font-sans`                    | デフォルトフォント      |
| `fontSize/sm`         | `text-sm`                      | 小サイズテキスト       |
| `borderRadius/full`   | `rounded-full`                 | 完全な角丸              |

---

## 4. コンポーネント命名と状態管理ルール

> 例：
> - `Button/Primary.Hover` → プライマリボタンのホバー状態
> - `Card/WithImage.Focused` → 画像付きカードのフォーカス状態

- コンポーネントの基本構造は `Block/Variant.State` の形式で命名します。
  - 例：`Button/Primary.Hover`, `Card/WithImage.Focused`
- 命名構成要素：
  - `Block`：UI部品の機能名（Button, Card, Formなど）
  - `Variant`：外観や用途の違い（Primary, Secondary, WithImageなど）
  - `State`：状態（Hover, Focused, Disabled など）

この構造を守ることで、Figma・tokens.json・CSS・AIコード生成などにおいて一貫した粒度で扱うことができます。

---



## 5. 基本コンポーネントのスタイル定義ルール

以下に示す定義は、セクション6の CSS 展開方針に基づいて構成されます。

### .button

```json
{
  "background-color": "var(--color-primary-500)",
  "color": "white",
  "padding": "var(--spacing-2) var(--spacing-4)",
  "border-radius": "var(--radius-md)",
  "font-family": "system-ui, sans-serif",
  "font-size": "0.875rem",
  "font-weight": "500",
  "transition": "background-color 0.3s ease",
  ":hover": {
    "background-color": "var(--color-primary-700)"
  }
}
```

### .input

```json
{
  "background-color": "white",
  "border": "1px solid var(--color-primary-300)",
  "padding": "var(--spacing-2)",
  "border-radius": "var(--radius-md)",
  "font-family": "system-ui, sans-serif",
  "font-size": "0.875rem",
  "transition": "border-color 0.2s ease",
  ":focus": {
    "border-color": "var(--color-primary-500)"
  }
}
```

---

## 6. トークンのCSS展開：@theme / @layer 指針

Tailwind CSS v4 では、`tailwind.config.js` を用いずに、CSSファイル内でカスタムプロパティとコンポーネントクラスを管理する手法が推奨されます。

### @theme

`@theme` ディレクティブを使うことで、トークンをCSS変数として定義します。

```css
@theme {
  --color-primary-500: oklch(60% 0.15 240);
  --spacing-4: 1rem;
  --radius-md: 0.375rem;
}
```

### @layer components

`@layer components` では、これらのトークンを使ったクラスを定義できます。

```css
@layer components {
  .button {
    background-color: var(--color-primary-500);
    padding: var(--spacing-2) var(--spacing-4);
    border-radius: var(--radius-md);
  }

  .input:focus {
    border-color: var(--color-primary-500);
  }
}
```

この構成により、Tailwind CSSのプリミティブなユーティリティと、デザイントークンを統合したコンポーネント設計が可能になります。





