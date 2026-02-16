# column/ — コラム記事ディレクトリ

## 概要
GitHub Pages（yoshidaagri.github.io/profile）のコラムコーナー。
Markdown下書き → HTML変換 → このディレクトリに配置 → git push で公開。

## ディレクトリ構成

```
column/
├── README.md           ← 本ファイル（運用ガイド）
├── index.html          ← コラム一覧ページ（カテゴリフィルター付き）
├── 001-xxx.html        ← 記事HTML
├── 002-xxx.html
├── 003-xxx.html
└── ...
```

**Markdown下書きの格納先**: `github_plan/columns/` （HTMLに変換する前の原稿）

## 記事追加の手順

### 1. Markdown下書きを作成する
`github_plan/columns/` に以下のフォーマットで作成する。

```markdown
---
title: 記事タイトル
date: YYYY-MM-DD
category: AI/DX | 経営 | 補助金 | 創業
description: 記事の概要（120字程度。SEOのmeta descriptionにも使用）
---

## はじめに
（本文）

## セクション1
（本文）

## まとめ
（本文）
```

**ファイル名規則**: `{3桁連番}-{英語スラッグ}.md`
- 例: `004-gas-gemini-automation.md`

### 2. HTMLに変換する
Claude Code のスキル `/publish-column` を使うか、手動で変換する。

#### スキル使用の場合
```
/publish-column github_plan/columns/004-gas-gemini-automation.md
```
→ 記事HTML作成 + index.html更新 + index.htmlのカード更新 を一括実行

#### 手動の場合（チェックリスト）

- [ ] 記事HTML作成（`column/{番号}-{スラッグ}.html`）
- [ ] `column/index.html` の記事リストに追加（**最新記事を先頭に**）
- [ ] `index.html` のコラムセクション（トップページ）のカード3枚を更新
- [ ] 前の記事の `column-nav-next` リンクを追加（前後ナビの接続）
- [ ] 新記事の `column-nav-prev` リンクを設定

### 3. git push する

```bash
git add column/ index.html
git commit -m "Add column: 記事タイトル"
git push
```

## 記事HTML テンプレート仕様

### head
- `<meta name="description">` : Markdownフロントマターの `description` を使用
- `<title>`: `{記事タイトル} | コラム | 吉田中小企業診断士事務所`
- CSS: `../assets/css/style.css`
- Fonts: Noto Sans JP + Fira Code
- favicon: `../assets/images/logo.png`

### ナビゲーション
- パスはすべて `../` プレフィックス（column/ からの相対パス）
- 「コラム」リンクに `class="nav-active"` を付与

### 記事ヘッダー
```html
<section class="column-article-header">
    <div class="container">
        <div class="column-article-meta">
            <span class="column-category cat-{カテゴリキー}">{カテゴリ名}</span>
            <time class="column-date" datetime="YYYY-MM-DD">YYYY.MM.DD</time>
        </div>
        <h1>記事タイトル</h1>
    </div>
</section>
```

### カテゴリキーとCSSクラス

| カテゴリ | キー | CSSクラス | data-category |
|---------|------|-----------|---------------|
| AI/DX | aidx | `cat-aidx` | `aidx` |
| 経営 | management | `cat-management` | `management` |
| 補助金 | subsidy | `cat-subsidy` | `subsidy` |
| 創業 | startup | `cat-startup` | `startup` |

### 記事本文
```html
<article class="column-article-body">
    <h2>...</h2>
    <p>...</p>
    <h3>...</h3>
    <blockquote>...</blockquote>
    <ul><li>...</li></ul>
</article>
```
- `column-article-body` 内では h2, h3, p, ul, ol, blockquote, strong, table にスタイルが適用済み

### 関連CTA
記事の内容に関連するサービスページへのCTAを配置する。

```html
<div class="column-related-cta">
    <h3>関連サービス名</h3>
    <p>説明文</p>
    <a href="../{ページ}.html" class="btn btn-primary">リンクテキスト</a>
</div>
```

### 前後ナビ
```html
<nav class="column-nav-links">
    <a href="{前の記事}.html" class="column-nav-prev">&larr; 前の記事: {タイトル}</a>
    <a href="{次の記事}.html" class="column-nav-next">次の記事: {タイトル} &rarr;</a>
</nav>
```
- 前/次がない場合は `<span></span>` で空要素

### フッター・CTA
全記事共通。お問い合わせフォームへのリンクを配置。

## index.html（一覧ページ）の更新

新記事を追加する際、`column/index.html` の `<div class="column-list">` 内に以下を追加する。
**最新記事を先頭（一番上）に配置** すること。

```html
<a href="{番号}-{スラッグ}.html" class="column-list-item" data-category="{カテゴリキー}">
    <div class="column-list-meta">
        <span class="column-category cat-{カテゴリキー}">{カテゴリ名}</span>
        <span class="column-list-date">YYYY.MM.DD</span>
    </div>
    <div class="column-list-body">
        <h3>記事タイトル</h3>
        <p>記事概要（description）</p>
    </div>
</a>
```

## トップページ（index.html）のコラムセクション更新

トップページには最新3件のカードが表示されている。新記事追加時は最も古いカードを差し替える。
カードの構造は `column-card` クラスを参照。

## 文体ガイドライン

- **人間が書いた風** に。AIっぽい無機質な文章は避ける
- 冒頭に **具体的なエピソード・体験談** を入れる（クライアントとの会話、実体験など）
- 一人称は「私」。読者への語りかけは「〜ではないでしょうか」「〜かもしれません」
- 専門用語には必ず平易な説明を添える
- 結論では冒頭のエピソードを回収する（伏線回収構造）
