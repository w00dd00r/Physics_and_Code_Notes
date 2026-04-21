## # 磁気スキルミオン相転移研究 ＆ 学習記録 運用マニュアル（2026年4月改訂版）

## 1. 開発・運用体制（最新版）
「同期」ではなく**「役割固定」**。特にWSでのVS Code SSH活用を主軸に置きます。

| デバイス | 役割 | 運用方法 |
| :--- | :--- | :--- |
| **WS (s248)** | **計算・開発の心臓** | **VS Code SSHで直接接続。** `Simulation/` で計算、`results/` (NAS) へ出力。 |
| **Linux PC** | **解析・可視化** | `Analysis/` でJuliaを実行。NASの生データを読み込み、図を生成。 |
| **NAS (Terastation)** | **巨大データ保管庫** | `/mnt/terastation_kido/` としてマウント。WSとLinux PCから共通参照。 |
| **Windowsノート** | **執筆・管理** | GitHub経由で `note.md` や図（PDF/PNG）のみ同期。LaTeX執筆。 |

---

## 2. ディレクトリ構成（確定版）

```text
Skyrmion_PhaseTransition_MC/
├── .git/
├── .gitignore               # results/ 内の巨大データを除外
├── README.md                # セットアップ備忘録
│
├── Simulation/              # 🟩 計算（WSで実行）
│   ├── src/                 # .cpp ソースコード（★ここへ移動済み）
│   ├── results@             # -> /mnt/terastation_kido/sim_results (★NASへの道)
│   ├── DM3dMC13_original.sh # 実行スクリプト（src/ 内を参照するよう修正）
│   └── (各実行スクリプト)
│
├── Analysis/                # 🟦 解析（Linux PCで実行）
│   ├── processing/          # c_Delaunay_3D.c など（★ここへ移動済み）
│   ├── analyze_stats.jl     # 統計処理メイン
│   ├── visualize_spins.jl   # スピン配置図生成
│   └── output/              # グラフ・統計量.dat（★これのみGit同期）
│
└── Thesis/                  # 🟧 論文・資料執筆
    └── (main.tex, figures/)
```

---

## 3. 実践：VS Code SSH 運用術（ここが重要！）

WSとNASが繋がった今、**「WS上のVS Code」**で作業するのが最も効率的です。

1.  **接続:** VS Codeの `Remote-SSH` で `kido@s248` に入る。
2.  **開く:** `/home/kido-rikuma/Research/Skyrmion_PhaseTransition_MC` を開く。
3.  **メリット:**
    * `Simulation/src/` のコードを書き換え、そのまま下のターミナルでコンパイル・実行できる。
    * `results/`（NAS）の中身をエクスプローラーでGUI操作（名前変更や削除）できる。
    * **Git操作もVS Code左側のメニューからポチポチするだけで完結する。**

---

## 4. 日々の研究ルーティン（黄金フロー）

### ① 計算を投げる（WS側）
1.  VS Code SSHでWSに入る。
2.  `Simulation/src/` のコードを修正。
3.  `.sh` スクリプトを実行。
    * `./DM3dMC13_original.sh`
    * データは自動で `results/` (NAS) へ。

### ② 解析する（Linux PC側）
1.  Linux PCで `git pull` して最新の解析スクリプト（Julia）を手元に持ってくる。
2.  NASのパスを読み込んで解析実行。
3.  できた図を `Analysis/output/` に保存。

### ③ 記録して同期（共通）
1.  `Simulation/results/` 内に `note.md` を作り、その時の物理的な考察をメモ。
2.  `git add .` -> `git commit` -> `git push`
3.  Windowsノートで `git pull` して、綺麗な図を見ながらLaTeXを書く。

---

## 5. 💡 注意事項（トラブル防止）
* **`.gitignore` の死守:** 絶対に `results/*.dat`（巨大データ）を `git add` しないこと。間違えて `push` するとGitHubの容量制限に引っかかります。
* **絶対パス vs 相対パス:** スクリプト内でのパス指定は、極力 `~/Research/...` などの絶対パスか、リポジトリルートからの相対パスで統一しましょう。
