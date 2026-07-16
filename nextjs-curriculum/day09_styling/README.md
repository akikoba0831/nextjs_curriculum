# Day 9: Tailwind CSSでのスタイリング

## 🎯 目標
Next.jsで標準的に使われるTailwind CSSの基本を身につける

## 📖 学ぶ内容
- クラス名だけでスタイリングする考え方
- レスポンシブ対応の基本

## ✅ 実施タスク

Day1で `create-next-app` の際にTailwindを選んでいれば、すでに使える状態になっています。

### 1. トップページをスタイリングしてみる

`app/page.tsx` を編集:

```tsx
export default function Home() {
  return (
    <main className="max-w-2xl mx-auto p-8">
      <h1 className="text-3xl font-bold text-blue-600">はじめてのNext.js</h1>
      <p className="mt-2 text-gray-600">これはトップページです。</p>
    </main>
  );
}
```

### 2. カードデザインの一覧に変更する(Day4のposts一覧を改良)

```tsx
<ul className="grid grid-cols-1 sm:grid-cols-2 gap-4">
  {posts.map((post) => (
    <li key={post.id} className="border rounded-lg p-4 shadow hover:shadow-md transition">
      <Link href={`/posts/${post.id}`} className="text-blue-600 hover:underline">
        {post.title}
      </Link>
    </li>
  ))}
</ul>
```

## ✅ 完了チェックリスト
- [ ] Tailwindのクラス名だけで色・余白・文字サイズを変更できた
- [ ] `sm:` などのレスポンシブ用の接頭辞を使えた
- [ ] 通常のCSSファイルとの違い(クラス名で完結する)を理解した
