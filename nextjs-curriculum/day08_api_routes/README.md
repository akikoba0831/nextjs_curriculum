# Day 8: API Routes (Route Handlers)

## 🎯 目標
Next.jsの中に簡易的なバックエンドAPIを作れるようになる

## 📖 学ぶ内容
- `route.ts` によるAPIエンドポイント作成
- GET / POST の実装方法

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
