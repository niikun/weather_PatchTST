# weather-patchtst-project

気象庁オープンデータ（東京・日別気温）を使った **PatchTST による翌7日間気温予測**。  
第3弾（LSTM）からの続き。

## 結果サマリー

| モデル | seq_len | パラメータ数 | MAE (℃) | RMSE (℃) |
|--------|---------|------------|---------|---------|
| LSTM（第3弾） | 30 | ~50k | 2.12 | 2.72 |
| PatchTST（本プロジェクト） | 60 | 71,367 | 2.04 | 2.64 |

## ⚠️ 設計変更メモ：途中で seq_len を変更

開発途中で入力窓サイズ（`seq_len`）を **30 → 60** に変更しました。

| 項目 | 初期設計 | 変更後 |
|------|---------|--------|
| `seq_len` | 30 | **60** |
| `stride` | 1 | **2** |
| `d_model` | 128 | 64 |
| `num_hidden_layers` | 3 | 2 |
| パッチ数 | 25 | **28** |

**理由**: PatchTST は長い文脈が有効（元論文は 336〜720 ステップ）なため、  
より長い入力窓を試した結果、精度が改善した。

- `02_dataset.ipynb`: **初期設計（seq_len=30）の説明・検証用**。パイプラインには含まれない。
- `03_train.ipynb`: **実際の学習**。seq_len=60 でデータを再生成してから学習。
- `04_compare.ipynb`: **評価・比較**。03 が保存した seq_len=60 データを使用。

## ノートブック構成

| ファイル | 内容 |
|---------|------|
| `01_setup.ipynb` | 環境確認・ライブラリインストール |
| `02_dataset.ipynb` | 初期設計の Shape 確認（参考用、seq_len=30） |
| `03_train.ipynb` | **本番学習**（seq_len=60、Early Stopping） |
| `04_compare.ipynb` | LSTM vs PatchTST 評価・比較 |

## データ

- 出所: 気象庁オープンデータ（東京・日別平均気温 2016〜2025）
- 前処理: MinMaxScaler、スライディングウィンドウ、train/val/test = 70/15/15%

## 環境

- Google Colab T4 GPU
- PyTorch + transformers（PatchTST）
- Python 3.x
