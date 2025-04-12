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

## 3. Figma変数の命名ルール

| Figma Variable | Tailwind Utility | 備考 |
|----------------|------------------|------|
| `spacing/4`    | `p-4`, `m-4`     | `1rem` |
| `color/primary`| `bg-primary`, `text-primary` | メインカラー |
| `radius/md`    | `rounded-md`     | `0.375rem` |
| `font/heading-md` | `text-lg font-bold` | 中見出し |
| `font/body-sm` | `text-sm font-sans` | 小文本 |

---

## 4. Figmaコンポーネントの命名ルール

- Semantic階層を `/` で区切る。
  - 例: `Card/WithCTA`, `Form/Input/WithLabel`
- 状態やバリアントは `.` で表す。
  - 例: `Button/Primary.Hover`

---

## 5. 共通UIクラス定義

Tailwind CSS v4 の思想に基づき、`@layer components` に加え `@theme` ディレクティブによるカスタムトークン定義をCSSファイル内で行う方式を採用します。これにより `tailwind.config.js` を廃止し、トークンやコンポーネント定義のすべてをCSSベースで記述可能になります。

### カラートークン定義例（@theme使用）
```css
@theme {
  --color-primary-500: oklch(60% 0.15 240);
  --color-primary-700: oklch(40% 0.15 240);
  --color-background: oklch(98% 0.01 260);
  --color-text: oklch(15% 0.02 260);
  --radius-md: 0.375rem;
  --spacing-2: 0.5rem;
  --spacing-4: 1rem;
  --spacing-6: 1.5rem;
}
```

### コンポーネント定義例
```css
@layer components {
  .button {
    background-color: var(--color-primary-500);
    color: white;
    font-size: 0.875rem;
    padding: var(--spacing-2) var(--spacing-4);
    border-radius: var(--radius-md);
    font-family: system-ui, sans-serif;
    transition: background-color 0.3s ease;
  }

  .button:hover {
    background-color: var(--color-primary-700);
  }
}
```

---

## 6. コンポーネントインベントリ

| クラス名 | 用途 |
|----------|------|
| `.button` | CTAボタン (主にPrimary) |
| `.input`  | テキスト入力、チェックボックス、ラジオなど `<input>` 要素全般 |
| `.select` | `<select>` 要素に適用される定義スタイル |
| `.card`   | 框付きレイアウト |
| `.badge`  | ステータスラベル |
| `.alert`  | フィードバック通知 |

---

## 7. ディレクトリ構成（予定）

```
.
├── tokens/              # JSON形式のデザイントークン定義
│   └── tokens.json
├── styles/
│   ├── theme.css        # @theme によるトークン変数定義
│   └── components.css   # @layer components によるUI定義
├── docs/
│   └── design-docs.md   # 設計ガイドライン（本ドキュメント）
├── README.md            # プロジェクト概要と導線
```

---

## 8. 今後の展開

- `tokens.json`の公開とGitHub管理
- ChatGPTによる制約付きコード生成プロンプト化
- Figma → tokens → コンポーネント → Storybook → 実装 の統一プロセス
- `.button`, `.input`, `.select`のCSS定義の詳細抽出
- OKLCHによるカラースケール作成ルールの整備
- Tailwind CSS v4 に最適化された `@theme` + `@layer` 中心のトークン＆コンポーネント設計へ全面移行

---
