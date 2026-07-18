# Day 6: レイアウトとMetadata

## 🎯 目標
共通レイアウトの入れ子構造と、SEO用のMetadata設定を理解する

## 📖 学ぶ内容
- ネストしたレイアウト(特定のフォルダだけ共通デザインを適用)
- `metadata` によるタイトル・説明文の設定

## 📚 解説

### レイアウトは「入れ子」かつ「維持」される
`app/layout.tsx`(全体共通)と`app/posts/layout.tsx`(postsだけ共通)を両方作ると、`/posts`配下のページは両方のレイアウトに包まれます。これは単に「HTMLが二重に囲まれる」だけでなく、**`/posts`から`/posts/1`に遷移してもレイアウト部分は再レンダリングされず、状態が保持される**という重要な特性があります。例えばレイアウトの中にClient Componentで作ったサイドバーの開閉状態があれば、投稿の詳細ページに移動してもその状態は保たれたままです。

この特性の代わりに、レイアウトには**`searchParams`が渡されない**という制約があります(ナビゲーションで再利用されるため、リクエストごとに変わる値を安全に受け取れない)。クエリパラメータが必要な処理は`page.tsx`側で行う必要があります。

### ⚠️ 重要な訂正: `generateMetadata` の `params` もPromiseである
Day5で学んだ`params`のPromise化は、`generateMetadata`関数にも同様に適用されます。

```tsx
export async function generateMetadata({ params }: Props) {
  const { id } = await params;
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
  const post = await res.json();
  return {
    title: post.title,
  };
}
```

### 静的`metadata`と`generateMetadata`の使い分け
- 内容が固定なページ(About、Contactなど)は、単純なオブジェクトの`export const metadata = {...}`で十分
- URLの値やfetch結果によってタイトルを変えたいページ(投稿詳細など)は、非同期関数`generateMetadata`を使う

親子のレイアウト/ページでmetadataが両方定義されている場合、子のmetadataが親の値を**上書き・マージ**します(例えば`title.template`を親で設定しておくと、子のタイトルの前後に共通の接頭辞・接尾辞を自動で付けられます)。今回は深入りしませんが、実務でサイト名を全ページのタイトルに付けたい場合などに使う仕組みです。

## ✅ 実施タスク

### 1. postsフォルダ専用のレイアウトを作る

`app/posts/layout.tsx` を新規作成:

```tsx
export default function PostsLayout({ children }: { children: React.ReactNode }) {
  return (
    <div>
      <p style={{ background: "#eee", padding: "8px" }}>📚 投稿セクション</p>
      {children}
    </div>
  );
}
```

`/posts` や `/posts/1` にアクセスすると、この見出しが共通で表示されるはずです。
`app/layout.tsx`(全体共通) と `app/posts/layout.tsx`(postsだけ共通) が
両方適用される「入れ子構造」になっています。

### 2. Metadataを設定する

`app/posts/page.tsx` に以下を追加:

```tsx
export const metadata = {
  title: "投稿一覧 | My Next.js App",
  description: "投稿の一覧ページです",
};
```

ブラウザのタブに表示されるタイトルが変わることを確認してください。

### 3. 詳細ページでは動的にMetadataを変える

`app/posts/[id]/page.tsx` に `generateMetadata` 関数を追加:

```tsx
export async function generateMetadata({ params }: Props) {
  const { id } = await params;
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
  const post = await res.json();
  return {
    title: post.title,
  };
}
```

## ✅ 完了チェックリスト
- [ ] ネストしたlayoutが正しく適用された
- [ ] 静的なMetadataを設定できた
- [ ] `generateMetadata` で動的にタイトルを変えられた

## 🧠 理解度チェック
1. `/posts`から`/posts/1`に遷移したとき、`app/posts/layout.tsx`は再レンダリングされるか説明せよ。
2. レイアウトが`searchParams`を受け取れない理由を、レイアウトの「維持される」という特性と結びつけて説明せよ。
3. 静的な`metadata`オブジェクトではなく`generateMetadata`関数を使うべきなのはどんなときか、具体例を挙げよ。
4. **予想→検証**: `app/posts/layout.tsx`の中にClient Componentで簡単なカウンター(Day3で作ったもの)を埋め込み、カウントを増やした状態で`/posts/1`へ遷移してみよ。カウントの値がどうなるか予想してから確認せよ。
