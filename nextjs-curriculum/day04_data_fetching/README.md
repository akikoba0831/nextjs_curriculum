# Day 4: データ取得とLoading UI

## 🎯 目標
Server Componentで外部データを取得し、読み込み中の画面も用意する

## 📖 学ぶ内容
- Server Componentで直接 `await fetch()` する
- `loading.tsx` による自動ローディング表示
- `fetch`のキャッシュ挙動と、SSR/SSG/ISRの違い

## 📚 解説

### なぜコンポーネント自体を`async function`にできるのか
Day3で学んだ通り、Server Componentはサーバー上でのみ実行されます。サーバー上でのコード実行なら「データが返ってくるまで待つ」ことに何の問題もないため、Reactは通常のJSXコンポーネントに加えて**`async function`のコンポーネント**を許可しています。これにより、これまでの `useEffect` + `useState` によるデータ取得(コンポーネントが一度描画された後に取得を開始し、ローディング状態を自前で管理する)ではなく、**「必要なデータが揃うまでレンダリングを進めない」**というシンプルなモデルでデータ取得が書けます。

### ⚠️ 重要な訂正: `fetch` はデフォルトでキャッシュされない
Next.js 13〜14の時代は「Server Componentの`fetch`はデフォルトでキャッシュされる」という仕様で広く知られていましたが、**このプロジェクトが使っているNext.js 15以降ではこの挙動が変わっており、`fetch`はデフォルトでキャッシュされません**。ネット上の古い記事やAIの回答は、この変更を反映していないことが多いので注意してください。

キャッシュしたい場合は明示的にオプションを指定する必要があります。

```tsx
// キャッシュしない(デフォルト。毎回サーバーにリクエストが飛ぶ)
const res = await fetch(url);

// ビルド時にキャッシュし、以後は再利用する
const res = await fetch(url, { cache: "force-cache" });

// 60秒ごとに再検証する(ISR的な挙動)
const res = await fetch(url, { next: { revalidate: 60 } });
```

### リクエストの重複排除(メモ化)
同じレンダーパスの中で、同じURL・同じオプションの`fetch`を複数のコンポーネントから呼んでも、Next.jsは実際のリクエストを1回にまとめてくれます。これを**リクエストメモ化**と呼びます。そのため「このデータ、他のコンポーネントでも使うから毎回propsで渡さないと」と気にする必要はなく、必要なコンポーネントでそれぞれ気軽に`fetch`して構いません。

### `loading.tsx` の正体
`app/posts/loading.tsx` を置くだけでローディング表示が自動化されるのは、Next.jsが内部でそのセグメントを **React の `<Suspense>`** で包んでいるためです。`Posts`コンポーネントの`await fetch()`が終わるまでの間、Next.jsは`loading.tsx`の内容を代わりに表示し、データが揃い次第本来のコンテンツに差し替えます。「魔法のファイル」ではなく、Reactの標準機能であるSuspenseの上に成り立つ仕組みだと理解しておくと、後々の学習(ネストしたSuspense境界など)につながります。

### SSR・SSG・ISR、結局何が違うのか
| 呼び方 | いつHTMLを作るか | このDayでの対応 |
|---|---|---|
| SSG (Static Site Generation) | ビルド時に1回だけ生成し、以後は使い回す | `fetch(url, { cache: "force-cache" })` |
| SSR (Server-Side Rendering) | リクエストが来るたびに毎回生成する | `fetch(url)`(デフォルト、キャッシュなし) |
| ISR (Incremental Static Regeneration) | 静的生成しつつ、一定時間ごとに裏側で再生成する | `fetch(url, { next: { revalidate: 秒数 } })` |

用語自体はPages Router時代からありますが、App Routerでは「ページ単位」ではなく**`fetch`呼び出し単位**でこれらを使い分けられる、という点が大きな進化です。

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

## 🧠 理解度チェック
1. このプロジェクトのNext.jsバージョンで、`fetch(url)`をオプションなしで呼んだ場合、結果はキャッシュされるか説明せよ。
2. `{ next: { revalidate: 60 } }` を付けた場合と付けない場合で、データの新しさとサーバー負荷にどんなトレードオフが生まれるか説明せよ。
3. **予想→検証**: `app/posts/page.tsx` の`fetch`の前に `await new Promise((resolve) => setTimeout(resolve, 2000));` を追加し、`loading.tsx`がどのタイミングで表示・非表示になるか予想してから実際に確認せよ。
4. 同じページ内の2つの別々のコンポーネントが、全く同じURLに対して`fetch`を呼んでいたとする。Next.jsは実際に何回サーバーにリクエストを送るか説明せよ。

## 💡 ポイント
Reactでは`useEffect`でデータ取得するのが一般的でしたが、Next.jsのServer Componentでは
コンポーネント自体を`async function`にして直接`await`するのが標準的な書き方です。
