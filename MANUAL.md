# typhoon_forecast_viewer — 手法・進め方の詳細

台風の予報円・進路線などを表示する、スマホ最適化されたWebアプリ。
既存の [typhoonTrackForecastViewer](/Users/masahiro/web/webapp/typhoonTrackForecastViewer/)
（PC向け・OpenLayersベースの高機能ビューア）を参考にしつつ、まずは最小構成から作る。

## 参考プロジェクトの棚卸し

### typhoonTrackForecastViewer（機能・計算ロジックの参考元）
- OpenLayers（`js/ol/`）+ jQuery/jQuery UIベースのPC向けWebアプリ。
- 実況・予報円・暴風域/強風域・進路線・海上警報等、多数の機能を持つ（`js/forecast.js`, `js/wind.js`,
  `js/sea_area_wind.js`, `js/boundary_300km.js` 等）。
- データ取得は `cgi/get_vptw6x_info_from_fcd-naps.php` 等、独自のCGI経由で
  「fcd-naps」というサーバーから取得している。この経路は気象業務支援センター等の
  商用データ配信を経由している可能性が高く、無料公開APIではないと考えられる。
  → **本プロジェクトではデータ取得経路をそのまま踏襲せず、気象庁の公開フィードに置き換える**。
- 参考にするのは「予報円・進路線の描画ロジック」「予報円と実況円の位置関係の計算」等の
  **計算・表示アルゴリズム**であり、データ取得部分ではない。

### mobile_app（モバイル最適化の参考元）
- React Native + Expo（TypeScript, expo-router）。JMA APIから直接データ取得し、
  天気予報・レーダー・キキクルタブを1つのアプリ内に統合。
- 地図系（レーダー・キキクル）はLeafletをHTML文字列化し、iPhoneではWebView・Web版ではiframeで表示。
- iPhoneネイティブアプリ・Android・Webの3プラットフォームに1つのコードベースで対応できる点が特徴。

### tide_viewer（Vanilla JS・シンプル構成の参考元）
- Vanilla JS / HTML / CSSのみ、ビルド不要。`index.html` + `css/` + `js/` のシンプルな構成。
- 観測所選択・localStorageでの状態復元・GitHub Pagesでの公開など、
  「フレームワーク不使用でもモバイルで快適に使える」実例。

## 技術方針（今回の決定）

**まずはVanilla JS（tide_viewer方式）でシンプルな第一歩を作る。**
React Native + Expo（mobile_app方式）への移行は、具体的な必要性が出てから再検討する。
評価の詳細は [plan.md](plan.md) を参照。

## 初期スコープ（Phase 1）

**予報円・進路線のみを表示する最小構成**から開始する。

- 表示するもの:
  - 現在の台風中心位置
  - これまでの実況の進路線
  - 今後の予報位置（各予報時刻の中心位置）
  - 予報円（存在する場合）
- 表示しないもの（後回し）:
  - 暴風域・強風域
  - 海上警報
  - 過去の複数台風の同時表示 等

## データソースの調査状況

> ⚠️ **2026-09-24 追記**: 気象庁は 2026-05-28 の防災気象情報の新体系移行で台風情報の配信先を変更した。旧 `information/data/typhoon.json` は 2026-05-27 のまま更新されない。**現行は `https://www.jma.go.jp/bosai/typhoon/data/targetTc.json`（発生中の台風一覧）と `typhoon/data/{TC番号}/specifications.json`・`forecast.json`**（形式は `common/jma_api_spec.md` 第8章）。以下の旧パスの記述は、実装時に置き換えること。

- 気象庁は台風情報を `https://www.jma.go.jp/bosai/information/data/typhoon.json`
  （発生中の台風一覧）と `https://www.jma.go.jp/bosai/information/data/typhoon/{fileName}`
  （個別台風の詳細）という無料公開JSONで配信している（[jma_mcp](../jma_mcp/)が既に利用中）。
- ただし確認した限り、この経路で得られるのは見出し・本文中心の「定型全般台風情報」であり、
  予報円の緯度経度・半径といった構造化データが直接JSONフィールドとして
  含まれているかは未確認（Phase0で要調査）。
- 気象庁のXML配信（`xml.kishou.go.jp`、[jma_mcp](../jma_mcp/)が警報等で参照している出典）にも
  台風の詳細予報（5日予報等）に対応するテレグラムコードが存在するはずで、
  こちらも合わせて調査する。
- **Phase0の実装タスク**として、「予報円の緯度経度・半径をどのJSON/XMLフィールドから
  取得できるか」を確定させることが最初の一歩になる。

## 段階的な進め方

### Phase 0: データソースの確定
- 気象庁の公開フィード（JSON/XML）から、予報円・進路点の緯度経度・半径が
  取得できるエンドポイント・フィールドを特定する。
- 取得できない場合、[jma_mcp](../jma_mcp/) 側の実装拡張も検討する。

### Phase 1: 最小構成の実装
- 地図上に実況の進路線・今後の予報位置・予報円を表示するだけのシンプルなビューア。
- スマホの小さい画面での閲覧を前提に、地図ライブラリ（Leaflet想定、tide_viewer/mobile_appと同じ）・
  レスポンシブCSS・タッチ操作を最初から意識する。

### Phase 2以降（機能追加）
- 暴風域・強風域の表示
- 過去の台風経路との比較
- 複数台風の同時表示
- 詳細は都度 [README.md](README.md) の「今後の拡張候補」に追記していく。

## 使用予定ライブラリ・技術

- Vanilla JS / HTML / CSS（フレームワーク不使用）
- 地図: Leaflet（tide_viewer・mobile_appと共通の選定）
- データ取得: 気象庁公開フィード（JSON/XML）。取得に `curl`/`wget` は使用しない
  （ブラウザの`fetch`を使うため、そもそも該当しない）。

## 注意事項

- 気象庁データの利用規約を遵守すること。
- データ取得経路が確定するまでは、実データの表示レイアウトのみをダミーデータで先行実装する
  というアプローチも選択肢になる。
