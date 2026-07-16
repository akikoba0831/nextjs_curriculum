# Day 7: フォームとServer Actions

## 🎯 目標
Next.js独自の「Server Actions」を使ってフォーム送信をサーバー側で処理する

## 📖 学ぶ内容
- Server Actionsとは何か(APIを別途作らずにサーバー処理ができる仕組み)
- `"use server"` の使い方

## ✅ 実施タスク

### 1. Server Actionを作る

`app/posts/actions.ts` を新規作成:

```tsx
"use server"; // ← この関数がサーバーで実行されることを示す

export async function createPost(formData: FormData) {
  const title = formData.get("title");
  console.log("新しい投稿タイトル:", title);
  // 本来はここでDB保存などを行う
}
```

### 2. フォームを作る

`app/posts/new/page.tsx` を新規作成:

```tsx
import { createPost } from "../actions";

export default function NewPost() {
  return (
    <form action={createPost}>
      <input type="text" name="title" placeholder="投稿タイトル" />
      <button type="submit">投稿する</button>
    </form>
  );
}
```

`http://localhost:3000/posts/new` にアクセスし、フォームを送信してみてください。
ターミナル(サーバー側のログ)に入力した内容が表示されればOKです。

## 💡 従来のReactとの違い
Reactだけの場合、フォーム送信は`onSubmit`でJavaScriptを使い、
別途APIを`fetch`で呼ぶ必要がありました。
Next.jsのServer Actionsでは、`action={関数名}`と書くだけでサーバー処理につながります。

## ✅ 完了チェックリスト
- [ ] `"use server"` の役割を説明できる
- [ ] フォーム送信でサーバー側の関数が実行されるのを確認した
- [ ] 従来のonSubmit方式との違いを理解した
