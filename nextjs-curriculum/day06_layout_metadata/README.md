# Day 6: レイアウトとMetadata

## 🎯 目標
共通レイアウトの入れ子構造と、SEO用のMetadata設定を理解する

## 📖 学ぶ内容
- ネストしたレイアウト(特定のフォルダだけ共通デザインを適用)
- `metadata` によるタイトル・説明文の設定

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
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.id}`);
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
