# OpenAI Whisper サンドボックス（日本語）

OpenAI のオープンソース Whisper を Docker で動かすプロジェクトです。

- https://github.com/openai/whisper

## 前提

- Docker
- Docker Compose v2

<details>
<summary>確認時のバージョン:</summary>
<pre>
❯ docker version
Client:
 Cloud integration: v1.0.35+desktop.10
 Version:           25.0.2
 API version:       1.44
 Go version:        go1.21.6
 Git commit:        29cf629
 Built:             Thu Feb  1 00:18:45 2024
 OS/Arch:           darwin/arm64
 Context:           desktop-linux

Server: Docker Desktop 4.27.1 (136059)
 Engine:
  Version:          25.0.2
  API version:      1.44 (minimum version 1.24)
  Go version:       go1.21.6
  Git commit:       fce6e0c
  Built:            Thu Feb  1 00:23:21 2024
  OS/Arch:          linux/arm64
  Experimental:     false
 containerd:
  Version:          1.6.28
  GitCommit:        ae07eda36dd25f8a1b98dfbf587313b99c0190bb
 runc:
  Version:          1.1.12
  GitCommit:        v1.1.12-0-g51d5e94
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
</pre>
</details>

## 利用可能なモデル

最新の情報・詳細は [`openai/whisper` のREADME](https://github.com/openai/whisper?tab=readme-ov-file) を確認してください。

|  サイズ  | パラメータ数 | 英語のみモデル | 多言語モデル | 必須 VRAM | 相対的速度 |
|:------:|:----------:|:------------------:|:------------------:|:-------------:|:--------------:|
|  tiny  |    39 M    |     `tiny.en`      |       `tiny`       |     ~1 GB     |      ~32x      |
|  base  |    74 M    |     `base.en`      |       `base`       |     ~1 GB     |      ~16x      |
| small  |   244 M    |     `small.en`     |      `small`       |     ~2 GB     |      ~6x       |
| medium |   769 M    |    `medium.en`     |      `medium`      |     ~5 GB     |      ~2x       |
| large  |   1550 M   |        N/A         |      `large`       |    ~10 GB     |       1x       |

日本語を処理したい場合は「英語のみモデル」ではなく「多言語モデル」を選択します。

## 使い方

Docker イメージをビルドします。

```bash
docker compose build
```

ビルド時にモデルのダウンロードを行うこともできます。
`compose.yaml` の `app` サービスの `build.args` の `models` にモデル名を指定してください。

```yaml
args:
  models: "tiny base small"
```

`whisper` コマンドに音声ファイルを渡して文字起こしをします。
各モデルは実行時に自動的にダウンロードされます。

```bash
cp myaudio.m4a ./services/app/
docker compose run --rm -it bash
```

以下コンテナ内 Bash で:

```bash
# 言語 `Japanese` を指定する:
# （ `--model` を指定しなければデフォルトで `medium` が使用される）
whisper myaudio.m4a --language Japanese

# モデル `small` を指定する:
whisper myaudio.m4a --language Japanese --model small

# モデルのキャッシュ保存ディレクトリを変更する:
mkdir -p ./.cache/whisper
whisper myaudio.m4a --language Japanese --model_dir ./.cache/whisper

# 出力フォーマットを .srt に変更する:
whisper myaudio.m4a --language Japanese --output_format srt
```

使い終わったら:

Docker リソースを閉じます。

```bash
docker compose down
```

