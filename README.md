# typhoon_forecast_viewer

台風の予報円・進路線などを表示する、スマホ最適化されたWebアプリ。

詳しくは [MANUAL.md](MANUAL.md)（手法・データソース調査）、[plan.md](plan.md)（技術選定の評価）を参照。

## 概要

- 気象庁の台風情報（公開JSON/XML）を使い、台風の実況・予報位置・予報円を地図上に表示する。
- 既存の [typhoonTrackForecastViewer](/Users/masahiro/web/webapp/typhoonTrackForecastViewer/)
  （PC向け高機能ビューア）の計算・描画ロジックを参考にしつつ、データ取得は気象庁の公開フィードに
  置き換える（参考元はfcd-naps経由の独自CGIで、公開APIではない可能性が高いため）。
- スマホの小さい画面で見やすいUIを、Vanilla JS + レスポンシブCSS（[tide_viewer](../tide_viewer/)方式）で実現する。
  React Native + Expo（[mobile_app](../mobile_app/)方式）への移行は、必要になった時点で再検討する（詳細は[plan.md](plan.md)）。
- まずは「予報円・進路線の表示」のみのシンプルな実装から始め、段階的に機能を追加していく。

## 進め方（サマリー）

1. **Phase 0**: 気象庁の公開フィードから予報円・進路点のデータが取得できるエンドポイントを確定する
2. **Phase 1**: 実況の進路線・予報位置・予報円のみを表示する最小構成を実装する
3. **Phase 2以降**: 暴風域・強風域、複数台風の同時表示等を追加していく

## ディレクトリ構成

```
typhoon_forecast_viewer/
├── README.md       # このファイル
├── CLAUDE.md        # Claude Code向けプロジェクト固有ルール
├── MANUAL.md         # 手法・データソース調査の詳細
├── plan.md           # Vanilla JS vs React Native+Expo の技術選定評価
├── css/              # スタイルシート（実装はこれから）
└── js/               # アプリロジック（実装はこれから）
```

## 参考プロジェクト

- [typhoonTrackForecastViewer](/Users/masahiro/web/webapp/typhoonTrackForecastViewer/) — 計算・描画ロジックの参考元（PC向け高機能ビューア）
- [tide_viewer](../tide_viewer/) — Vanilla JS・モバイル対応構成の参考元
- [mobile_app](../mobile_app/) — React Native + Expoによるモバイルアプリ化の参考元（将来の選択肢）
- [jma_mcp](../jma_mcp/) — 気象庁台風情報JSONの既存利用実績

## 今後の拡張候補

- 暴風域・強風域の表示
- 過去台風との経路比較
- 複数台風の同時表示
- （将来）React Native + Expoへの移行 or mobile_appへの統合（[plan.md](plan.md)参照）

## ステータス

2026-07-15 新規作成。データソースの確定・実装はこれから。
