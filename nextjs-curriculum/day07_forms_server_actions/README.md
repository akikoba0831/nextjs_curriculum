# Day 7: フォームとServer Actions

## 🎯 目標
Next.js独自の「Server Actions」を使ってフォーム送信をサーバー側で処理する

## 📖 学ぶ内容
- Server Actionsとは何か(APIを別途作らずにサーバー処理ができる仕組み)
- `"use server"` の使い方

## 📚 解説

### Server Actionsは「関数をそのままサーバーに送る」仕組み
通常のReactでフォーム送信を扱う場合、`onSubmit`でJavaScriptを実行し、`fetch`で自作のAPIエンドポイントを呼ぶ、という2つの部品(クライアントのイベントハンドラとサーバーのAPI)を別々に用意する必要がありました。

Server Actionsは、`"use server"`を付けた関数を**フォームの`action`属性に直接渡すだけ**で、フォーム送信→サーバーでの関数実行→結果に応じた再レンダリングまでを1つの仕組みで完結させます。裏側では、そのフォームが送信された時点でNext.jsが自動的にその関数をサーバーへのリクエストとして呼び出しています。「別途APIを作らなくていい」というのは、API相当の処理をNext.jsが自動生成してくれている、と理解すると腑に落ちます。

### ⚠️ セキュリティ上の注意: Server Actionは事実上のpublicエンドポイント
Server Actionは「フォームに紐付いているから安全」というわけではありません。実体としては、ブラウザから直接呼び出し可能な1つのHTTPエンドポイントとして公開されます。つまり、悪意のあるユーザーがフォームを経由せず直接そのアクションを叩くことも技術的には可能です。

そのため、**Server Actionの関数内では必ず認証・認可・入力値の検証を行う**必要があります。「フォームがログイン画面の裏にあるから」「クライアント側でバリデーションしているから」といった理由だけで安全だと考えてはいけません。

### 今回のコード例の限界(あえて残してある不完全な部分)
`createPost`は`console.log`で終わっており、実際には何もデータを保存していません。仮にデータベースに保存する処理を足したとしても、**`revalidatePath("/posts")`のような再検証処理を呼ばない限り、Day4で作った一覧ページ(`/posts`)はキャッシュされたままの古いデータを表示し続けます**。「データを更新する処理」と「そのデータを表示しているページのキャッシュを更新する処理」は別物である、という点は実務で非常につまずきやすいポイントです。理解度チェックでこの点を発展課題として扱います。

### Server Actionsは順番に実行される
複数のServer Actionを同時に呼び出しても、Reactはそれらを並列(`Promise.all`のように)ではなく**逐次(順番)**に実行するよう設計されています。これはフォーム送信のような「順序が重要な操作」を安全に扱うための仕様です。

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

## 🧠 理解度チェック
1. Server Actionが「フォームに紐付いているから安全」とは言えない理由を説明せよ。
2. `createPost`関数の中でユーザー認証のチェックを一切していないとする。この状態を本番運用するとどんな問題が起きうるか説明せよ。
3. **発展課題**: `createPost`に`revalidatePath("/posts")`(`next/cache`からimport)を追加し、投稿後に一覧ページのデータが更新されるかどうかを予想してから確認せよ(データ保存自体は行わず、`revalidatePath`だけ試してみるのでも構わない)。
4. 通常のReact(`onSubmit` + 自作API)とServer Actionsとで、書くコードの量やファイル数にどんな違いが出るか比較せよ。
