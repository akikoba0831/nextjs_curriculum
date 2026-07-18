# Day 8: API Routes (Route Handlers)

## 🎯 目標
Next.jsの中に簡易的なバックエンドAPIを作れるようになる

## 📖 学ぶ内容
- `route.ts` によるAPIエンドポイント作成
- GET / POST の実装方法

## 📚 解説

### Route Handlerは「Pages Router時代のAPIルート」の後継
以前のNext.js(Pages Router)では`pages/api/hello.ts`のような形でAPIエンドポイントを作っていました。App Routerでは同じ役割を`app/api/hello/route.ts`が担い、これを**Route Handler**と呼びます。`page.tsx`がUIを定義する規約なのに対し、`route.ts`は同じフォルダにHTTPメソッド名(`GET`, `POST`, `PUT`, `DELETE`など)の関数をexportすることでAPIとして機能する規約です(同じフォルダに`page.tsx`と`route.ts`は共存できません)。

### Server Actions・Route Handler・Server Componentの`fetch`、どれを使うべきか
Day7でServer Actionsを学んだ今、「サーバー側で処理をする方法」が複数出てきて混乱しやすいタイミングです。目的別に整理すると次のようになります。

| やりたいこと | 使うもの |
|---|---|
| 自分のページ内のフォーム送信・データ更新 | Server Actions(Day7) |
| ページ表示のためのデータ取得だけ | Server Componentで直接`await fetch()`(Day4) |
| 外部クライアント(モバイルアプリ、他サービス)からも呼ばれる汎用API | Route Handler |
| Webhook(Stripeやgithubなどの外部サービスからの通知)を受け取る | Route Handler |

「とりあえずAPIを作る」のではなく、**呼び出し元がNext.jsアプリの外にあるかどうか**が最初の判断基準になります。今回作る`/api/hello`のように、自分のアプリ内のClient Componentから呼ぶだけなら、実務では代わりにServer Actionsを使うケースも多いです。ここでは学習のためあえてRoute Handler経由で呼び出します。

### GETハンドラもキャッシュされ得る
Route Handlerの`GET`関数は、条件によってはNext.jsによってキャッシュされることがあります(動的な値を使わない単純なGETなど)。「APIだから常に最新のデータを返す」と思い込まず、Day4で学んだキャッシュの考え方はRoute Handlerにも共通する、と覚えておいてください。

## ✅ 実施タスク

### 1. APIエンドポイントを作る

`app/api/hello/route.ts` を新規作成:

```tsx
import { NextResponse } from "next/server";

export async function GET() {
  return NextResponse.json({ message: "こんにちは、Next.js API!" });
}
```

`http://localhost:3000/api/hello` にブラウザでアクセスし、JSONが表示されればOK。

### 2. POSTリクエストも実装する

同じファイルに追記:

```tsx
export async function POST(request: Request) {
  const body = await request.json();
  return NextResponse.json({ received: body });
}
```

### 3. Client Componentから呼び出してみる

`app/api-test/page.tsx` を新規作成:

```tsx
"use client";
import { useState } from "react";

export default function ApiTest() {
  const [result, setResult] = useState("");

  const handleClick = async () => {
    const res = await fetch("/api/hello", { method: "POST", body: JSON.stringify({ name: "太郎" }) });
    const data = await res.json();
    setResult(JSON.stringify(data));
  };

  return (
    <div>
      <button onClick={handleClick}>APIを呼ぶ</button>
      <p>{result}</p>
    </div>
  );
}
```

## ✅ 完了チェックリスト
- [ ] GETリクエストでAPIからデータを取得できた
- [ ] POSTリクエストでデータを送信できた
- [ ] Client ComponentからAPIを呼び出す流れを理解した

## 🧠 理解度チェック
1. Day7のServer Actionsと今回のRoute Handlerは、どちらも「サーバー側の処理」を書ける点で似ている。両者をどんな基準で使い分けるべきか説明せよ。
2. 自分のアプリの中のフォーム送信だけが目的なら、Route Handlerを使わずServer Actionsだけで完結できる場合が多い。それでもRoute Handlerが必要になるのはどんなケースか、具体例を1つ挙げよ。
3. `app/api/hello/route.ts` と同じフォルダに `page.tsx` を置くとどうなるか予想し、実際に試して確認せよ。
4. 今回作った`/api/hello`のPOSTハンドラに認証チェックが無いとする。外部から誰でも呼び出せてしまうことのリスクを説明せよ(Day7のServer Actionsのセキュリティの話と共通する点に触れよ)。
