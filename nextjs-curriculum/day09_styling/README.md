# Day 9: Tailwind CSSでのスタイリング

## 🎯 目標
Next.jsで標準的に使われるTailwind CSSの基本を身につける

## 📖 学ぶ内容
- クラス名だけでスタイリングする考え方
- レスポンシブ対応の基本

## 📚 解説

### ⚠️ 注意: このプロジェクトはTailwind CSS v4(ゼロコンフィグ)
`package.json`を見るとわかる通り、このプロジェクトは**Tailwind CSS v4**を使っています。v3までは`tailwind.config.js`でテーマや対象ファイルを設定する必要がありましたが、**v4はゼロコンフィグ・CSSファースト**な設計になり、`app/globals.css`内の`@import "tailwindcss"`という1行と、必要なら`@theme`ブロックだけで完結します。ネット上のTailwindチュートリアルや解説記事の多くはまだv3を前提にしており、`tailwind.config.js`の編集方法などを紹介していますが、それらはこのプロジェクトにはそのまま当てはまりません。「動かない」と感じたら、まずv3向けの情報を見ていないか疑ってください。

### ユーティリティファーストという設計思想
Tailwindは「見た目ごとにCSSクラスを新しく定義する」のではなく、`text-blue-600`(青文字)や`p-8`(padding)のような**小さな役割の単位(ユーティリティクラス)を組み合わせて**スタイルを組み立てます。

メリットは、別ファイル(`.css`)を行き来せずJSXの中だけでスタイルの意図が完結すること、そして「同じような見た目のコンポーネントで少しずつ違うCSSクラス名が量産される」問題を防げることです。デメリットは、`className`が長くなりがちで、慣れないうちは可読性が落ちて見えることです。

### レスポンシブの考え方(モバイルファースト)
`sm:`, `md:`, `lg:`のような接頭辞は「この画面幅**以上**になったら適用する」という意味です。何も付けないクラス(例: `grid-cols-1`)がベースのスタイルとして常に適用され、`sm:grid-cols-2`はそれを画面が広いときだけ上書きします。これは**モバイルファースト**という考え方で、「まず一番小さい画面を基準に組み、大きい画面向けに追加していく」設計です。

| 接頭辞 | 目安の画面幅 |
|---|---|
| (なし) | 全ての画面幅 |
| `sm:` | 640px以上 |
| `md:` | 768px以上 |
| `lg:` | 1024px以上 |

## ✅ 実施タスク

Day1で `create-next-app` の際にTailwindを選んでいれば、すでに使える状態になっています。

### 1. トップページをスタイリングしてみる

`app/page.tsx` を編集:

```tsx
export default function Home() {
  return (
    <main className="max-w-2xl mx-auto p-8">
      <h1 className="text-3xl font-bold text-blue-600">はじめてのNext.js</h1>
      <p className="mt-2 text-gray-600">これはトップページです。</p>
    </main>
  );
}
```

### 2. カードデザインの一覧に変更する(Day4のposts一覧を改良)

```tsx
<ul className="grid grid-cols-1 sm:grid-cols-2 gap-4">
  {posts.map((post) => (
    <li key={post.id} className="border rounded-lg p-4 shadow hover:shadow-md transition">
      <Link href={`/posts/${post.id}`} className="text-blue-600 hover:underline">
        {post.title}
      </Link>
    </li>
  ))}
</ul>
```

## ✅ 完了チェックリスト
- [ ] Tailwindのクラス名だけで色・余白・文字サイズを変更できた
- [ ] `sm:` などのレスポンシブ用の接頭辞を使えた
- [ ] 通常のCSSファイルとの違い(クラス名で完結する)を理解した

## 🧠 理解度チェック
1. このプロジェクトでTailwindの設定を変えたいとき、`tailwind.config.js`を編集する方法がなぜ通用しない(もしくは前提が違う)のか説明せよ。
2. `grid-cols-1 sm:grid-cols-2`という書き方を、モバイルファーストの考え方に基づいて説明せよ(画面が狭いときと広いときでそれぞれ何列になるか)。
3. **予想→検証**: ブラウザの開発者ツールで画面幅を640px前後に変えながら`app/posts/page.tsx`のカード一覧を観察し、レイアウトが何px付近で切り替わるか確認せよ。
4. ユーティリティファーストなCSSの書き方のメリットとデメリットを、それぞれ1つ以上挙げよ。
