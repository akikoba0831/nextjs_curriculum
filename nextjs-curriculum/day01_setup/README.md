# Day 1: 環境構築 & プロジェクト作成

## 🎯 目標
Next.jsのプロジェクトを作成し、フォルダ構成の意味を理解する

## 📖 学ぶ内容
- `create-next-app` でのプロジェクト作成
- `app` フォルダの役割
- 開発サーバーの起動方法

## ✅ 実施タスク

### 1. プロジェクトを作成する
このカリキュラムのフォルダとは別に、作業用フォルダを用意してそこで実行してください。

```bash
npx create-next-app@latest my-nextjs-app
```

聞かれる質問には、以下のように答えるのがおすすめです(初心者向け標準構成)。

```
TypeScript を使いますか?          → Yes
ESLint を使いますか?              → Yes
Tailwind CSS を使いますか?        → Yes
`src/` ディレクトリを使いますか?   → No (最初はシンプルに)
App Router を使いますか?          → Yes (必須)
import alias をカスタマイズしますか? → No
```

### 2. 開発サーバーを起動する

```bash
cd my-nextjs-app
npm run dev
```

ブラウザで `http://localhost:3000` を開き、Next.jsの初期画面が表示されればOKです。

### 3. フォルダ構成を見てみる

VSCodeでプロジェクトを開き、以下のファイル・フォルダを確認してください。

```
my-nextjs-app/
  app/
    layout.tsx   ← 全ページ共通のレイアウト
    page.tsx     ← "/" のトップページ
    globals.css  ← 全体のスタイル
  public/        ← 画像などの静的ファイル
  package.json   ← 依存パッケージの管理
```

### 4. トップページを書き換えてみる

`app/page.tsx` を開き、中身を以下のようにシンプルにしてみましょう。

```tsx
export default function Home() {
  return (
    <main>
      <h1>はじめてのNext.js</h1>
      <p>これはトップページです。</p>
    </main>
  );
}
```

保存すると、ブラウザが自動で更新されます(Fast Refresh機能)。

## ✅ 完了チェックリスト
- [ ] `npm run dev` でローカルサーバーが起動できた
- [ ] `app/page.tsx` を編集して画面に反映されるのを確認した
- [ ] `layout.tsx` と `page.tsx` の役割の違いを説明できる

## 💡 つまずきやすいポイント
- Node.jsのバージョンが古いとエラーになることがあります(`node -v` で確認、18以上推奨)
- 保存しても反映されない場合は、ターミナルでエラーが出ていないか確認してください
