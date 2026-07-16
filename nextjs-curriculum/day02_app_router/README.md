# Day 2: App Routerの基本

## 🎯 目標
ファイルを置くだけでページが増える「ファイルベースルーティング」を理解する

## 📖 学ぶ内容
- `app` フォルダの中にファイルを作ってページを追加する方法
- `next/link` を使ったページ遷移
- `<a>` タグとの違い

## ✅ 実施タスク

### 1. Aboutページを作る

`app/about/page.tsx` というファイルを新規作成してください。

```tsx
export default function About() {
  return (
    <main>
      <h1>About</h1>
      <p>このサイトについての説明ページです。</p>
    </main>
  );
}
```

→ `http://localhost:3000/about` にアクセスして表示されればOK

### 2. ナビゲーションリンクを作る

`app/layout.tsx` を開き、`<body>` の中にナビゲーションを追加します。

```tsx
import Link from "next/link";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="ja">
      <body>
        <nav>
          <Link href="/">Home</Link> | <Link href="/about">About</Link>
        </nav>
        {children}
      </body>
    </html>
  );
}
```

### 3. Contactページも同様に作ってみる
`app/contact/page.tsx` を自分で作成し、ナビゲーションにもリンクを追加してみましょう。

## 💡 なぜ `<a>` ではなく `next/link` を使うのか
- `<a>` タグ: ページ全体を再読み込みする(遅い)
- `next/link`: 必要な部分だけ差し替える(速い、SPAのような動き)

## ✅ 完了チェックリスト
- [ ] `/about` ページを新規作成できた
- [ ] `next/link` でページ間を遷移できた
- [ ] `<a>` タグと`next/link`の違いを説明できる
