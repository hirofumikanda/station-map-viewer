# Station Map Viewer

[MapLibre GL JS](https://maplibre.org/) と [PMTiles](https://docs.protomaps.com/pmtiles/) を使って、駅データ（`stations.pmtiles`）を地図上に表示する静的ビューアです。

## 機能

- ベースマップに OpenStreetMap の標準タイルを使用（透過度 0.5）
- 駅をポイントで表示し、`importance`（重要度）に応じて色分け
  - 重要度が高い: 赤系 / 低い: 青系
- 駅名（`name`）をラベル表示
- 駅をクリックすると、全プロパティをポップアップで表示
- 右下に凡例を表示
- URL ハッシュに地図の位置・ズームを反映

## ファイル構成

| ファイル | 説明 |
| --- | --- |
| `index.html` | ビューア本体 |
| `stations.pmtiles` | 駅のベクタータイル（レイヤー名: `station`、ズーム 4〜14） |

## ローカルでの実行

PMTiles は HTTP Range リクエストで読み込むため、`file://` ではなく HTTP サーバー経由で開いてください。Range リクエストに対応したサーバーが必要です。

```sh
npx http-server -p 8080
```

その後、<http://localhost:8080/> を開きます。

> Python 標準の `python -m http.server` は Range リクエストに対応していないため、PMTiles の読み込みに失敗します。

## `stations.pmtiles` のプロパティ

| プロパティ | 説明 |
| --- | --- |
| `id` | 駅グループコード |
| `name` | 駅名 |
| `passengers_per_day` | 1日あたりの乗客数 |
| `operator_count` | 事業者数 |
| `line_count` | 路線数 |
| `importance` | 重要度（0〜5）。[JEV](https://jev.typesafe.ai/)（`jev-latest`）の `importance` 質問（`score` 型）のスコア |
| `confidence` | JEV の回答の信頼度（0〜1） |

### 重要度（`importance`）について

`importance` は、JEV に駅の情報（駅名・乗降客数・路線数・事業者数）を渡し、「日本全国を対象とした一般的な地図における、駅名注記としての重要度」を 6 段階の基準（0: 非常に局所的な駅 〜 5: 全国的に重要な主要駅）で評価させたときの **JEV の `score`** をそのまま使用しています。乗降客数だけでなく、路線数、事業者数、広域交通上の役割、駅の認知度が総合的に評価されます。LLM による評価のため、結果には揺らぎや誤りが含まれる場合があります。

## 使用ライブラリ・データ

- [MapLibre GL JS](https://maplibre.org/) 6.x
- [PMTiles](https://github.com/protomaps/PMTiles) 4.3.0（`esm.sh` 経由）
- ベースマップ: © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright)
- 駅データ: [国土数値情報（駅別乗降客数データ）](https://nlftp.mlit.go.jp/ksj/gml/datalist/KsjTmplt-S12-2024.html)（国土交通省）を加工して作成
- 重要度: JEV の `score` を使用
- ラベル用フォント: `tile.openstreetmap.jp` のグリフ配信（Noto Sans JP）
