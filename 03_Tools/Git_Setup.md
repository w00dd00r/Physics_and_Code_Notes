# Git セットアップ備忘録

## 1. ユーザー設定（リポジトリごと）
このリポジトリで作業する前に、以下のコマンドで自分を登録する。
- `git config user.email "自分のメアド"`
- `git config user.name "Kido"`

## 2. PC 内での保存場所（重要！）
OneDrive の同期エラーを避けるため、以下の場所にフォルダを作る。
`C:\Users\woodd\Research`

## 3. 基本の 3 ステップ（毎日使う）
1. `git add .` （変更を準備する）
2. `git commit -m "メッセージ"` （記録を確定する）
3. `git push origin main` （GitHub に送る）