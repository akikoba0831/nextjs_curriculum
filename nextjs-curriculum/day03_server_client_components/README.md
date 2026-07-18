# Day 3: Server / Client Componentsの使い分け

## 🎯 目標
「サーバーで動く部分」と「ブラウザで動く部分」を実際に切り分けて体験する

## 📖 学ぶ内容
- デフォルトはServer Component
- `"use client"` の役割
- `useState` や `onClick` はClient Componentでしか使えない

## 📚 解説

### Server Componentは「サーバーで動くReact」ではない
これまでのReactは、コンポーネントがブラウザ上で実行され、必要ならAPIを叩いてデータを取ってくるものでした。App Routerの**Server Component**はこれと全く違うモデルです。Server Componentはビルド時またはリクエスト時に**サーバー上でのみ**実行され、その結果(RSC Payloadと呼ばれるコンポーネントツリーのシリアライズされた表現)がブラウザに送られます。ブラウザはそれを元にHTMLを組み立てて表示するだけで、Server Component自体のコードはブラウザに一切送られません。

これが Day1 で書いた `app/page.tsx` が特別な理由です。App Routerでは**全てのコンポーネントはデフォルトでServer Component**です。`useState`や`onClick`のような「ブラウザ側で状態を持つ・イベントに反応する」機能が必要なときだけ、明示的に`"use client"`を宣言してClient Componentに切り替えます。

### なぜバンドルサイズが減るのか
Server Componentの中でしか使わない処理(例えば巨大な日付処理ライブラリやMarkdownパーサーなど)は、そのコードごとサーバー側だけで完結し、**クライアント向けのJavaScriptバンドルには一切含まれません**。従来のReact(SPA)では「表示に使う可能性がある全てのコード」をブラウザに送る必要がありましたが、Server Componentは「表示結果(RSC Payload)だけ」を送るため、ユーザーがダウンロードするJSの量を大きく減らせます。これがApp Routerのパフォーマンス上のメリットの核心です。

### `"use client"` は「ファイルの中身をブラウザで動かす宣言」ではなく「境界線の宣言」
`"use client"` はよく「このコンポーネントをブラウザで実行する」と説明されますが、より正確には**「ここから先はサーバー/クライアントの境界を超える」という宣言**です。`"use client"`を付けたファイルからimportされる関数・コンポーネントは、それがどんなに小さくても**芋づる式に全てクライアントバンドルに含まれます**。これは初心者が誤解しやすいポイントで、「`"use client"`を付けたファイルの中身だけがクライアントに送られる」のではなく、「そのファイルが依存しているモジュールグラフ全体がクライアントに送られる」という点に注意してください。

この特性から、実務では**`"use client"`をなるべく葉(リーフ)に近い、小さいコンポーネントにだけ付ける**のがセオリーです。今回の`Counter`のように「状態を持つ最小限の部品」だけをClient Componentにして、それを大きなServer Componentのページに埋め込む、という構成がまさにその実践例です。

### ハイドレーション
ブラウザに届いたHTMLは最初は「ただの見た目」で、ボタンを押しても何も起きません。ブラウザ側でReactのJavaScriptが読み込まれ、Client Componentに対してイベントハンドラなどを結びつける処理を**ハイドレーション(hydration)**と呼びます。`Counter`コンポーネントのボタンが押せるようになるのは、このハイドレーションが完了した後です。Server Componentの部分にはこの処理が発生しない(そもそもブラウザにJSが送られていない)ため、ページ全体でハイドレーションが必要な範囲を最小化できます。

## ✅ 実施タスク

### 1. カウンターコンポーネントを作る(Client Component)

`app/counter.tsx` を新規作成:

```tsx
"use client"; // ← ブラウザで動かすための宣言

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>カウント: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

### 2. トップページに読み込む(Server Component)

`app/page.tsx` を編集:

```tsx
import Counter from "./counter";

export default function Home() {
  return (
    <main>
      <h1>はじめてのNext.js</h1>
      <Counter />
    </main>
  );
}
```

`page.tsx` 自体には `"use client"` を書いていない点に注目してください。
Server Componentの中にClient Componentを組み込む、という一般的なパターンです。

### 3. わざとエラーを起こしてみる(理解を深めるため)

`"use client"` を消した状態で `useState` を書くとどうなるか、試してみましょう。
エラーメッセージを読んで、なぜ起きるのか理解してみてください。

## ✅ 完了チェックリスト
- [ ] `"use client"` を付けたコンポーネントでボタンが動いた
- [ ] `"use client"` を消すとエラーになることを確認した
- [ ] 「Server Componentの中にClient Componentを置く」構成を理解した

## 🧠 理解度チェック
1. `Counter`コンポーネントに`"use client"`が必要な理由を、`useState`と`onClick`という2つの観点から説明せよ。
2. `"use client"`を`counter.tsx`の代わりに`app/page.tsx`(Homeコンポーネント)の先頭に付けた場合、Homeコンポーネント全体がClient Componentになる。このときバンドルサイズやパフォーマンスにどういう影響が出るか予想せよ。
3. Server Componentのコードは「クライアントバンドルに一切含まれない」とはどういう意味か、具体例(ライブラリなど)を挙げて説明せよ。
4. **予想→検証**: `counter.tsx`から`"use client"`を消してブラウザで確認するとエラーが出る。表示されるエラーメッセージを読み、なぜそのエラーが起きるのかを自分の言葉で説明せよ(エラーメッセージにヒントが書かれている)。

## 💡 覚えておきたい判断基準
| 必要なもの | 使うもの |
|---|---|
| クリックやフォーム入力 | Client Component |
| `useState` / `useEffect` | Client Component |
| データ取得だけ | Server Component |
