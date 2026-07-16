# Day 3: Server / Client Componentsの使い分け

## 🎯 目標
「サーバーで動く部分」と「ブラウザで動く部分」を実際に切り分けて体験する

## 📖 学ぶ内容
- デフォルトはServer Component
- `"use client"` の役割
- `useState` や `onClick` はClient Componentでしか使えない

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

## 💡 覚えておきたい判断基準
| 必要なもの | 使うもの |
|---|---|
| クリックやフォーム入力 | Client Component |
| `useState` / `useEffect` | Client Component |
| データ取得だけ | Server Component |
