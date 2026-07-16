# Day 4: データ取得とLoading UI

## 🎯 目標
Server Componentで外部データを取得し、読み込み中の画面も用意する

## 📖 学ぶ内容
- Server Componentで直接 `await fetch()` する
- `loading.tsx` による自動ローディング表示

## ✅ 実施タスク

### 1. 一覧ページを作る

`app/posts/page.tsx` を新規作成:

```tsx
type Post = {
  id: number;
  title: string;
};

export default async function Posts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=10");
  const posts: Post[] = await res.json();

  return (
    <main>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </main>
  );
}
```

`http://localhost:3000/posts` にアクセスして一覧が表示されればOK。

### 2. Loading UIを追加する

同じ `app/posts/` フォルダに `loading.tsx` を作成:

```tsx
export default function Loading() {
  return <p>読み込み中...</p>;
}
```

このファイルがあるだけで、データ取得中に自動的に表示されます。
(ローカルだと速すぎて見えない場合は、`fetch`の前に以下を一時的に入れてみてください)

```tsx
await new Promise((resolve) => setTimeout(resolve, 1000)); // 動作確認用の遅延
```

## ✅ 完了チェックリスト
- [ ] Server Componentで `fetch` してデータを表示できた
- [ ] `loading.tsx` を使ってローディング表示ができた
- [ ] なぜServer Componentで`useEffect`を使わなくて良いのか説明できる

## 💡 ポイント
Reactでは`useEffect`でデータ取得するのが一般的でしたが、Next.jsのServer Componentでは
コンポーネント自体を`async function`にして直接`await`するのが標準的な書き方です。
