# Day 5: 動的ルーティング

## 🎯 目標
URLの一部が変化するページ(詳細ページなど)を作れるようになる

## 📖 学ぶ内容
- `[id]` フォルダによる動的ルート
- `params` の受け取り方(Promiseとして渡される)

## 📚 解説

### `[id]` フォルダの意味
Day2で「フォルダ構造 = URL構造」と学びましたが、`[id]`のように角括弧で囲んだフォルダ名は特別な意味を持ちます。これは**動的ルートセグメント**と呼ばれ、`/posts/1`, `/posts/2`, `/posts/abc` のように、そのURL部分がどんな値でも同じ`page.tsx`にマッチするようになります。マッチした実際の値(`1`や`abc`)は`params`というpropを通してコンポーネントに渡されます。

### ⚠️ 重要な訂正: `params` はPromiseである
以前のNext.js(〜v14)では`params`は`{ id: string }`のような普通のオブジェクトとして同期的に受け取れましたが、**Next.js 15以降(v15.0.0-RC)で破壊的変更が入り、`params`は`Promise`になりました**。これはこのプロジェクトが使っているNext.js 16でも同様です。そのため`await params`で中身を取り出す必要があります。

```tsx
type Props = {
  params: Promise<{ id: string }>;
};

export default async function PostDetail({ params }: Props) {
  const { id } = await params;
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
  const post = await res.json();

  return (
    <main>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </main>
  );
}
```

なぜPromise化されたのか: App Routerはページ全体が揃うのを待たず、確定した部分から段階的にHTMLを組み立てていく「ストリーミング」を重視する方向に進化しています。`params`を最初からPromiseにしておくことで、動的な値の解決を待たずにレンダリングの他の部分を先に進められるようになります。ネット上の記事やAIの回答には、この変更前の同期的な書き方(`params: { id: string }`)がまだ多く残っているため、書くときは必ずこのプロジェクトのNext.jsバージョンの挙動を優先してください。

## ✅ 実施タスク

### 1. 詳細ページ用のフォルダを作る

`app/posts/[id]/page.tsx` を新規作成(`[id]`という名前のフォルダがポイント):

```tsx
type Props = {
  params: Promise<{ id: string }>;
};

export default async function PostDetail({ params }: Props) {
  const { id } = await params;
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
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
- [ ] `await params` でURLの値を受け取れた
- [ ] 一覧 → 詳細 のページ遷移ができた

## 🧠 理解度チェック
1. `params`がなぜ`{ id: string }`ではなく`Promise<{ id: string }>`として渡されるようになったのか、その狙いを説明せよ。
2. `app/posts/[id]/page.tsx` を `app/posts/[postId]/page.tsx` にリネームした場合、コード内のどこを合わせて変更する必要があるか説明せよ。
3. **予想→検証**: `PostDetail`コンポーネントで`await params`をせずに`params.id`と直接書くとどうなるか予想し、実際に試してエラー内容を確認せよ。
4. `/posts/999999`(存在しないid)にアクセスするとどうなるか予想し、実際に確認せよ。今の実装のままで問題ないか、改善の余地はあるか考えてみよ。

## 💡 補足
`[id]` は名前を自由に決められます(例: `[slug]`, `[postId]`)。
フォルダ名とコード内で`await params`から取り出す際のプロパティ名を一致させることが重要です。
