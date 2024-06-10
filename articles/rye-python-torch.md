---
title: "Ryeを用いたPyTorchおよびPyG環境構築"
emoji: "🌟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rye", "cuda", "cudnn", "torch", "torchgeometric"]
published: true
---

## 要約

Windows上のCUDA環境において、Ryeを用いてPyTorchおよびPyG (PyTorch Geometric) のライブラリをインストールすることができた。`pyproject.toml`にソースを設定することが必要となる。

## Ryeについて

RyeはPythonのバージョン管理とライブラリ管理の両方を1つで行えるツール。Rustで内部実装されている。ここではインストール方法には触れない。インストール済みであるとして進める。

- [Rye](https://rye.astral.sh/)

## CUDA環境の構築

以下が必要となる。

- NVIDIAディスプレイドライバーのインストール
- NVIDIA CUDA Toolkit のインストール
- NVIDIA cuDNN のインストール

この3つは組み合わせの相性があり、以下のページでサポートされている組み合わせが記載されている。

- [Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/latest/reference/support-matrix.html)

ここでは、最新のドライバーと CUDA Toolkit 12.1、cuDNN 9.2.0 をインストールする。PyTorchが対応しているバージョンについても確認しておく必要がある。最新の CUDA Toolkit だとPyTorchが対応していないことがある。

### NVIDIAディスプレイドライバのインストール

NVIDIAの[Geforceドライバー](https://www.nvidia.com/ja-jp/geforce/drivers/)のページから、対応するグラフィックボード製品の最新のドライバーをダウンロードし、インストールする。

### NVIDIA CUDA Toolkit のインストール

NVIDIA CUDA Toolkit はNVIDIAのGPUを用いて汎用的な計算を行うための開発環境。PyTorchの安定版が対応しているバージョンが12.1であるため、それをインストールする。[CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive) のページから CUDA Toolkit 12.1.1 のインストーラーをダウンロードし、インストールする。

### NVIDIA cuDNN のインストール

NVIDIA cuDNN はNVIDIAが提供する深層学習向けのライブラリ。こちらも CUDA Toolkit のバージョンに対応するものをインストールする。[cuDNN Archive](https://developer.nvidia.com/cudnn-archive) のページから cuDNN 9.2.0 の圧縮ファイルをダウンロードし、手動でインストールする。

cuDNN 9.2.0 をダウンロードする際は、Tarを選択し圧縮ファイルをダウンロードする。その後、圧縮ファイルを解凍し、`bin`、`include`、`lib`をそのままToolkitのインストール先 (`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1`) に上書きコピーする。

### 環境変数の設定

以下がシステム環境に設定されているか確認する。設定されていなければ設定する。

- `Path`
  - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1\bin`
  - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1\libnvvp`
- `CUDA_HOME`
  - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1`
- `CUDA_PATH`
  - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1`
- `CUDA_PATH_V12_1`
  - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1`
- `CUDNN_PATH`
  - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1`

PCを再起動して、環境変数を反映させる。

## Python仮想環境の構築

### プロジェクトフォルダを作成

仮想環境を配置するための任意の名前のフォルダを作成する。ここでは「ドキュメント」は以下に`venv`フォルダを作成する。Dropboxなどのクラウドで同期する環境配下に作成するのは避けた方が良い。

### Pythonのインストール

`venv`フォルダをエクスプローラーで開き、`Shift`+右クリックメニューの「ターミナルで開く」を選択する。ターミナルが起動し、カレントディレクトリが`venv`となっていることを確認する。

最初にRye自体をアップデートしておく。以下のコマンドを実行する。

```powershell
rye self update
```

以下のコマンドを実行し、仮想環境の初期化を行う。

```powershell
rye init
```

フォルダ内に、いくつかのフォルダとファイルが作成される。次にPytyonのバージョンを指定する。指定できるPythonの一覧は以下で表示できる。

```powershell
rye toolchain list --include-downloadable
```

ここでは Python 3.12.3 を用いることにする。

```powershell
rye pin 3.12.3
```

次のコマンドを実行し、現在の設定を同期させる。

```powershell
rye sync
```

### ライブラリのインストール

`rye add`と`rye remove`を用いて、PyTorchなどのライブラリを追加、削除していく。最初に開発時にしか用いないライブラリを`--dev`オプションを付けて指定していく。

```powershell
rye add --dev pip ruff 'spyder-kernels==2.5.*'
```

`spyder-kernels==2.5.*`は開発環境としてSpyderを使用したいために必要。Spyderを使わないならば不要。

次にPyTorchなどのライブラリを追加する。PyTorchとPyGをこの方法で追加する前にいくつかの設定が必要になる。[PyTorch公式サイト](https://pytorch.org/get-started/locally/)でPyTorchのインストール方法を確認すると以下のようになっていることが分かる。

```powershell
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

ここでソースとなる`https://download.pytorch.org/whl/cu121`の情報をRyeに設定しておく必要がある。また、PyGについても[PyG公式サイト](https://pytorch-geometric.readthedocs.io/en/latest/install/installation.html)によるとインストール方法は以下のようになっている。

```powershell
pip install torch_geometric

# Optional dependencies:
pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-2.3.0+cu121.html
```

これらのソースの情報は`pyproject.toml`に記述する。`pyproject.toml`をテキストエディタで開き、以下を追加して上書き保存する。

```toml
[[tool.rye.sources]]
name = "torch"
url = "https://download.pytorch.org/whl/cu121"
type = "index"

[[tool.rye.sources]]
name = "pytorch-geometric"
url = "https://data.pyg.org/whl/torch-2.3.0+cu121.html"
type = "find-links"
```

この設定を行ったあと、以下コマンドでTorchおよびPyGをインストールする。

```powershell
rye add torch torchvision torchaudio torch_geometric pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv
```

そのほか、必要に応じてライブラリを追加する。

```powershell
rye add pandas matplotlib seaborn scikit-learn statsmodels xgboost
```

設定を同期させる。

```powershell
rye sync
```

## 動作確認

`venv`フォルダ直下に以下の内容で`torch_test.py`を作成する。

```python:torch_test.py
import torch

# CUDAが利用可能か確認
if torch.cuda.is_available():
    print(" CUDA is available. Version:", torch.version.cuda)
else:
    print(" CUDA is not available.")

# cuDNNが利用可能か確認
if torch.backends.cudnn.is_available():
    print("cuDNN is available. Version:", torch.backends.cudnn.version())
else:
    print("cuDNN is not available.")
```

ターミナルから以下のコマンドを実行する。

```powershell
rye run python ./torch_test.py
```

CUDAとcuDNNがともに`available`になっていることを確認する。

## 関連記事

- [NVIDIA ドライバ，NVIDIA CUDA ツールキット 11.8，NVIDIA cuDNN v8.9.7 のインストール手順（Windows 上）](https://www.kkaneko.jp/tools/win/cuda.html)
- [RyeによるPython環境構築 - 理系学生日記](https://kiririmode.hatenablog.jp/entry/20240105/1704466799)
- [pythonパッケージ管理ツールryeを使う - 肉球でキーボード](https://nsakki55.hatenablog.com/entry/2023/05/29/013658)
- [Ryeで構築するPytorch+CUDA環境 #Python - Qiita](https://qiita.com/nyanko-box/items/52aace19cb99fb1146c2)
