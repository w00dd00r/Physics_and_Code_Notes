# 磁気スキルミオン相転移研究 ＆ 学習記録 運用マニュアル

## 0. 基本戦略（2つのリポジトリの使い分け）
研究の効率化と、将来に向けた知識の蓄積を両立させるため、用途の異なる**2つのGitHubリポジトリ**を作成して運用します。

1. **`Skyrmion_PhaseTransition_MC` (Private設定)**
   * **目的:** 特定の研究成果（論文・計算結果）を出すための「戦場」。守秘義務があるため非公開。
2. **`Physics_and_Code_Notes` (Public設定推奨)**
   * **目的:** 物理の理論やプログラミングの知見を蓄積する「一生モノの財産」。将来のポートフォリオとしても活用。

---

## 1. 開発・運用体制（デバイスの役割分担）
「同期」させるのではなく、**「役割を固定してアクセスする」**運用を基本とします。

| デバイス | 役割 | 運用方法 |
| :--- | :--- | :--- |
| **Linuxパソコン** | **メイン司令塔・解析機** | WSへSSH接続して計算を指令。NASのデータを読み込んでJulia/Cで解析・プロット。 |
| **ワークステーション(WS)** | **計算専用エンジン** | C++の重いシミュレーションを実行。生データをNAS/ローカルに生成。 |
| **NAS** | **巨大データ保管庫** | Git管理外の生データ（数GB〜数TB）をすべて保管。WSとLinux PCから共通パスで参照。 |
| **Windowsノート** | **執筆・サブ司令塔** | GitHubからノートとグラフ画像だけを受け取り、LaTeXで論文・ゼミ資料を作成。 |
| **GitHub** | **知識とコードのハブ** | ソースコード、解析スクリプト、研究・学習ノート（`.md`）、完成グラフ画像。 |

---

## 2. ディレクトリ構成（統一された階層構造）

### A. 研究用リポジトリ (`Skyrmion_PhaseTransition_MC`)
「ディレクトリ構造がそのまま解析ロジックになる」ように配置します。

```text
Skyrmion_PhaseTransition_MC/      # Gitリポジトリのルート
├── .gitignore                    # ★最重要：生データを無視し、ノートと解析結果だけ同期
├── README.md                     # 実行環境やライブラリの備忘録
│
├── Simulation/                   # 🟩 1. 計算（純粋な数値計算）
│   ├── src/                      # original.cpp, restart.cpp
│   ├── run_multi_size.sh         # 連続実行スクリプト
│   └── results/                  # 【Git管理外】生データ出力先
│       └── 202604XX_multi_size/
│           ├── run_all.log       # 計算ログ（★Git同期）
│           └── note.md           # 💡 物理的な気づきを書くノート（★Git同期）
│
├── Analysis/                     # 🟦 2. 解析（統計処理・描画）
│   ├── Makefile                  # 📍 全解析・描画を一括制御する司令塔
│   ├── processing/               # 【前処理】生データ -> 統計量.dat
│   ├── plotting/                 # 【描画】統計量.dat -> 図
│   └── output/                   # 📍 最終生成物（★.datと図のみGit同期）
│
└── Thesis/                       # 🟧 3. 論文・資料執筆
    ├── main.tex                  # 執筆の大元
    └── figures/                  # Analysis/output/figures/ から同期
```

### B. 学習用リポジトリ (`Physics_and_Code_Notes`)
後から検索しやすく、動作が重くならないように分類します。

```text
Physics_and_Code_Notes/
├── 01_Physics/              # 物理の理論メモ
│   ├── Statistical_Mechanics/
│   ├── Active_Matter/
│   └── Phase_Transition/
├── 02_Programming/          # 言語ごとのTips
│   ├── Julia/
│   ├── Cpp/
│   └── Python/
├── 03_Tools/                # ツールの設定備忘録
│   ├── Git_SSH_Setup.md     # 📍 今回覚えた環境構築手順など！
│   └── LaTeX_Snippets.tex   # よく使う数式テンプレート
└── 04_Seminar_Materials/    # ゼミで使った汎用的な資料（研究の生データ抜き）
```

---

## 3. 第1段階：研究拠点の構築（セットアップ）

### ① GitHubアカウントとメールアドレスの設定
* **アカウント作成:** 個人のメインメアド（Gmail等）で作成。
* **大学メアドの追加:** `Settings -> Emails` から追加・認証（学生特典を受けるため）。
* **リポジトリ設定:** 公式な記録を残したいリポジトリ内でのみ以下を実行。
  ```bash
  git config user.email "your-name@university.ac.jp"
  ```

### ② Windowsノートでの「箱」作り（初回のみ）
1. **GitHub上で作成:** `Skyrmion_PhaseTransition_MC` (Private) と `Physics_and_Code_Notes` (Public) の空リポジトリを作成。
2. **ローカルへClone:** WindowsPC（OneDrive外）の適当な場所でターミナルを開き実行。
   ```bash
   git clone https://github.com/あなたのID/Skyrmion_PhaseTransition_MC.git
   ```
3. **フォルダとファイル作成:** `Simulation`, `Analysis`, `Thesis` フォルダを作る。リポジトリ直下に完全版の `.gitignore` を保存する。
4. **最初のPush:**
   ```bash
   git add .
   git commit -m "Initial setup with .gitignore"
   git push origin main
   ```

### ③ Linux PC ＆ WS の準備
1. 各マシンで `git clone` して手元に持ってくる。
2. **パスワードなしログイン:** Linux PCからWSへSSH公開鍵を登録。
3. **NASマウント:** Linux PCとWSの両方でNASをマウントし、共通の絶対パスにする。
4. **tmux:** WSに仮想端末 `tmux` を導入（接続が切れても計算を継続させるため）。

---

## 4. テクニカル・ルール（自動化と統一）

> **「Analysisディレクトリに入って make したら必要な図が全て揃う」状況を維持する**

* **基準パスの統一:** スクリプト内のパスは「Analysisフォルダを基準とした相対パス」で記述する。
* **One-command 解析:** `make` を叩けば、未解析データの処理から図の生成まで全自動で行う状態を作る。
* **命名規則の連動:** `plot_energy.gp` が `energy.pdf` を出力するように名前を揃える。
* **図の同期:** `Analysis/output/figures/` から `Thesis/figures/` へのコピーは手動ではなくスクリプト化する。

---

## 5. 第2段階：日々の研究ルーティン（運用フロー）

### 1. 計算（Linux PC → WS）
* Linux PCからWSにSSH接続し、`tmux` 内で計算プログラム（`original.cpp` 等）を実行。
* 生データはGitで無視され、NAS（またはローカル）に安全に蓄積される。

### 2. 考察と同期（WS / Linux PC）
* 計算フォルダ内に `note.md` を作成し、パラメータ（$D$, $h$, $T$）や相転移の兆候などの気づきをメモ。
* `git push` で `note.md` とログだけをGitHubへ送る。

### 3. 解析と描画（Linux PC）
* Linux PCで `Analysis/` に移動し、`make` を実行（JuliaとCが走る）。
* 図のクオリティに納得したら `git push` し、GitHub経由で全デバイスに配布する。

### 4. 執筆・学習（Windowsノート）
* Windowsで `git pull` して最新の図と考察ノートを手元に揃え、`Thesis/` 内のLaTeXを執筆・コンパイルする。
* 役立つ知見が得られたら、`Physics_and_Code_Notes` リポジトリ側にMarkdownで書き残し、`push` しておく。