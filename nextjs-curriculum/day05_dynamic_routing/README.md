# Day 5: 動的ルーティング

## 🎯 目標
URLの一部が変化するページ(詳細ページなど)を作れるようになる

## 📖 学ぶ内容
- `[id]` フォルダによる動的ルート
- `params` の受け取り方

## ✅ 実施タスク

### 1. 詳細ページ用のフォルダを作る

`app/posts/[id]/page.tsx` を新規作成(`[id]`という名前のフォルダがポイント):

```tsx
type Props = {
  params: { id: string };
};

export default async function PostDetail({ params }: Props) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.id}`);
  const post = await res.json();

  return (
    <main>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </main>
  );
}
```

`http://localhost:3000/posts/1` のようにアクセスすると、該当する投稿が表示されます。

### 2. 一覧ページから詳細ページにリンクを貼る

Day4で作った `app/posts/page.tsx` を編集し、各投稿をリンクにします。

```tsx
import Link from "next/link";

// ...
<li key={post.id}>
  <Link href={`/posts/${post.id}`}>{post.title}</Link>
</li>
```

## ✅ 完了チェックリスト
- [ ] `[id]` フォルダで動的ページを作れた
- [ ] `params.id` でURLの値を受け取れた
- [ ] 一覧 → 詳細 のページ遷移ができた

## 💡 補足
`[id]` は名前を自由に決められます(例: `[slug]`, `[postId]`)。
フォルダ名とコード内の `params.〇〇` の名前を一致させることが重要です。
