# Day 10: Vercelへのデプロイ

## 🎯 目標
作ったアプリを実際にインターネット上に公開する

## 📖 学ぶ内容
- GitHubリポジトリの作成
- Vercelとの連携によるデプロイ

## ✅ 実施タスク

### 1. GitHubにプッシュする

```bash
git init
git add .
git commit -m "first commit"
```

GitHub上で新しいリポジトリを作成し、指示されたコマンドでプッシュしてください。

### 2. Vercelでデプロイする

1. https://vercel.com にアクセスし、GitHubアカウントでログイン
2. 「Add New Project」から、先ほど作ったリポジトリを選択
3. 設定はそのままで「Deploy」をクリック

数分待つと、`https://your-app.vercel.app` のようなURLでアプリが公開されます。

### 3. コードを更新して自動デプロイを確認する

適当な文言を変更してGitHubにpushしてみてください。
Vercelが自動で再デプロイしてくれるのを確認しましょう。

## ✅ 完了チェックリスト
- [ ] GitHubにコードをプッシュできた
- [ ] Vercelでアプリを公開できた
- [ ] コード変更 → push → 自動デプロイの流れを体験した

## 🎉 お疲れ様でした!
ここまでで、Next.jsの基本(ルーティング、Server/Client Components、データ取得、
Server Actions、API Routes、スタイリング、デプロイ)を一通り体験しました。

次のステップとしては以下がおすすめです:
- 認証機能(NextAuth.js / Auth.js)
- データベース連携(Prisma + PostgreSQLなど)
- 状態管理ライブラリとの組み合わせ(Zustandなど)
