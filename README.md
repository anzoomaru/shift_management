# 筒井動物病院 シフト管理システム（静的サイト）

## プロジェクト概要
- **目的**: 従業員のシフト提出・閲覧、およびオブザーバーによる閲覧を行うためのフロントエンド画面を提供
- **主な機能**: ログイン（デモ）、従業員シフト画面、オブザーバー閲覧画面

## 現在完了している機能
- ロゴ画像を犬の足跡ロゴに変更
- ログイン画面（GAS連携認証 + ID/パスワードの空白・大小文字ゆらぎ吸収）
- 従業員画面（カレンダー、シフト提出・取消 UI、他従業員シフト閲覧）
- オブザーバー画面（閲覧専用カレンダー）
- ログイン画面から「閲覧専用で入る」ボタンでゲスト閲覧
- ログイン時に従業員データ未取得の場合はエラーメッセージ表示
- 管理者画面（休診日設定・従業員管理・有給/休日数設定）
- ログアウト機能
- カレンダー描画の高速化（従業員名の一括取得・キャッシュ）
- 読み込み中のローディング表示（画面暗転 + Loading）
- 特定日（3/2, 3/3, 3/4, 3/9, 3/10）の初期配置データを自動削除
- 月変更時に休日数をリセット（将来は管理者設定予定）
- 他従業員シフトの手動読み込みボタンを追加（読み込み遅延対策）
- 休み指定などで再描画しても他従業員シフトを保持表示
- 未設定表示を「獣医師不在」「看護師不在」に変更（赤文字）
- 休診日（closures）を考慮したカレンダー表示（週末は自動休診なし）
- 管理者の「水曜・祝日を休診に設定」ボタン（解除ボタンなし、設定維持）
- 管理者で設定した有給残数が従業員画面に反映
- 管理者画面の従業員管理：指定休日数適用のON/OFF、曜日一括休み申請
- 管理者画面の休診日指定は「休診日を保存」ボタンで一括保存（保存済み/未保存の表示あり）
- 管理者画面のJS二重宣言を解消し、ログイン後のカレンダー描画が止まる問題を修正
- 管理者の休診日操作（クリック/曜日一括/祝日反映）の高速化とシフト削除の一括処理
- GASのCORS対策としてAPI更新処理はGET(action=create/delete)に変更（未対応だと保存失敗）
- 水曜・祝日休診の月でも、指定解除した日は休診扱いにならない（除外対応）
- カレンダー描画最適化（DocumentFragment + 日付セルマップ化で再描画高速化）
- 従業員画面の休日クリックはセルのみ更新し体感速度を改善

## 現在の機能エントリURI（パス）
- `/index.html`
  - ログイン画面
  - パラメータ: なし
- `/employee.html`
  - 従業員画面（シフト提出・取消）
  - パラメータ: なし
- `/observer.html`
  - オブザーバー画面（閲覧専用）
  - パラメータ: なし
- `/admin.html`
  - 管理者画面（休診日設定・従業員管理・有給/休日数設定）
  - パラメータ: なし

## 未実装の機能
- 実データとの連携（保存・削除処理は実装、管理者の設定機能は一部実装）
- 権限管理の詳細化（ロールごとの遷移制御強化）

## 推奨される次の開発ステップ
- テーブルスキーマの定義（employees, shifts, closures）
- 画面間の共通レイアウトやスタイルの共通化
- 例外時のUIメッセージ/エラー表示の改善
- 従業員一覧のページング対応（大量データ時）

## データモデル / ストレージ
- **使用テーブル**: `employees`, `shifts`, `closures`, `employee_settings`, `month_settings`
- **ストレージ**: Google Sheets + Google Apps Script（GAS）API
- **備考**: APIベースURL `https://script.google.com/macros/s/AKfycbx7V_oWv7oT1S3BnfG7WpTFCOIeVhl2K2SxndJ6UTKnsHljrx23SJpre2LhQvy6cKw2Rw/exec` を使用。`?table=shifts` などのクエリで取得・保存。休診日は `closures`、従業員の有給は `employee_settings`、月別休日数は `month_settings`。祝日は https://holidays-jp.github.io/api/v1/date.json を参照。ログインは `employees` の `password` 列（任意追加）と `role` 列で判定。**CORS対策のためPOSTは `application/x-www-form-urlencoded` と `action=delete` を使用**。

## 公開URL
- **Production**: なし（未デプロイ）
- **API**: Google Apps Script
  - Base: `https://script.google.com/macros/s/AKfycbx7V_oWv7oT1S3BnfG7WpTFCOIeVhl2K2SxndJ6UTKnsHljrx23SJpre2LhQvy6cKw2Rw/exec`
  - 例: `?table=shifts`

## 連絡・運用メモ
- デモログイン: `observer@observer / observer123`
- 管理者: `employees` の `role=admin` を付けたログインID（例: `takashi`）
