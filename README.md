# Design Token Mapping Guidelines

このリポジトリは、Figmaで変数化されたデザイントークンとTailwind CSS v4 のユーティリティクラスを連携させ、  
**一貫性・再現性・再利用性のあるUIデザインと実装**を実現するためのデザインシステムの基盤を提供します。

---

## 🎯 主な目的

- Figmaの変数（spacing, color, radius, typographyなど）を設計起点として活用
- Tailwind CSS v4 に準拠（`@theme` + `@layer` による構成）
- OKLCHカラーによる視認性と論理的な色階調
- W3C Design Tokens Draft 互換のトークン構造
- `.button`, `.input`, `.select` など再利用可能なUIパターンの標準化

---

## 📁 ディレクトリ構成（予定）

```
.
├── tokens/              # JSON形式のデザイントークン定義
│   └── tokens.json
├── styles/
│   ├── theme.css        # @theme によるトークン変数定義
│   └── components.css   # @layer components によるUI定義
├── docs/
│   └── design-docs.md   # 設計ガイドライン（技術仕様）
├── README.md            # この概要ドキュメント
```

---

## 📘 詳細な設計仕様

トークン構成・命名ルール・カラースケール・コンポーネント定義についての詳細は  
👉 [`docs/design-docs.md`](./docs/design-docs.md) をご参照ください。

---

## 🚀 今後の展開予定

- Figma Plugin や Style Dictionary との統合
- Storybook 等によるビジュアルガイドの追加
- ChatGPT等AIとの連携による自動UI生成支援
