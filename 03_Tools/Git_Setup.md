# Git/GitHub 操作マニュアル

研究ノートとコードを安全に管理するためのガイドです。

## 1. 初期セットアップ（新しい環境のみ）
新しいPCやWSで作業を始める前に、一度だけ実行します。
```bash
# ユーザー登録（自分の名前とメールアドレス）
git config --global user.email "自分のメアド"
git config --global user.name "Kido"

# 保存場所の確認（重要：OneDrive同期を避ける場所！）
# C:\Users\woodd\Research\Physics_and_Code_Notes
```

---

## 2. 作業を始める前（最新状態の取得）
他のPCやGitHub上での変更を取り込みます。
```bash
# 現在の場所がリポジトリ内であることを確認してから実行
git pull origin main
```
> **💡 ヒント：もし黒い画面（Vim）が出たら**
> 慌てずにキーボードで `:wq` と打って `Enter` を押してください。これで「保存して終了」できます。

---

## 3. 作業が終わった後（保存とアップロード）
作業を終える際、必ずこの3ステップを実行します。

### ステップA：変更を準備する（add）
```bash
# 全ての変更（LaTeXソースやPDF）を追加する場合
git add .

# 特定のファイルだけを慎重に追加する場合
git add 01_Physics/Non_equilibrium_Physics/*.pdf
git add .gitignore
```

### ステップB：記録を確定する（commit）
```bash
git commit -m "メッセージ内容（例：ランジュバン方程式の導出を追加）"
```

### ステップC：GitHubへ送る（push）
```bash
git push origin main
```

---

## 4. 困った時のトラブルシューティング

### Q. "fatal: not a git repository" と出る
* **原因:** ターミナルがリポジトリの「本拠地」にいません。
* **対策:** `cd` コマンドで移動してください。
    ```powershell
    cd C:\Users\woodd\Research\Physics_and_Code_Notes
    ```

### Q. "rejected ... (fetch first)" と出る
* **原因:** GitHub側に、手元にはない新しいデータ（READMEの編集など）があります。
* **対策:** 一度 `pull` してから、再度 `push` してください。
    ```bash
    git pull origin main
    # その後、もう一度 push
    git push origin main
    ```