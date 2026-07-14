# CLAUDE.md — typhoon_forecast_viewer

## プロジェクト概要
台風の予報円・進路線などを表示する、スマホ最適化されたWebアプリ。
詳細は [README.md](README.md) / [MANUAL.md](MANUAL.md) / [plan.md](plan.md) を参照。

## 方針
- 技術スタックはVanilla JS / HTML / CSS（プロジェクト群共通方針）。React Native + Expoへの
  移行は必要性が出てから再検討する（[plan.md](plan.md)参照）。今は導入しない。
- データ取得は気象庁の公開JSON/XMLフィードのみを使う。
  参考元 [typhoonTrackForecastViewer](/Users/masahiro/web/webapp/typhoonTrackForecastViewer/) の
  fcd-naps経由CGIは踏襲しない（公開APIではない可能性が高いため）。参考にするのは
  計算・描画ロジックのみ。
- 実装は「Phase0（データソース確定）→ Phase1（予報円・進路線のみの最小構成）→
  Phase2以降（暴風域・強風域等の追加）」の順で段階的に進める。
- 地図ライブラリは[tide_viewer](../tide_viewer/) / [mobile_app](../mobile_app/)と同じLeafletを想定。

## 技術スタック
- Vanilla JS / HTML / CSS（フレームワーク不使用）
- 地図: Leaflet
- データ取得: 気象庁公開フィード（`fetch` API使用、curl/wgetは不使用・そもそも該当しない）

## 注意事項
- 気象庁データの利用規約を遵守する。
- スマホでの表示確認（レスポンシブ・タッチ操作）を都度行う。
- データソースの構造（予報円半径等のフィールド）が未確定なため、Phase0で必ず調査してから実装に入ること。

## コーディング規約
- コメントは日本語で記述
- インデント: スペース2つ（JS/HTML/CSS）
- ファイル名: キャメルケース

## GitHub更新ルール
- ファイルを変更・追加した場合は、必ず `git add` → `git commit` → `git push` までを行うこと（push前にユーザー確認）
