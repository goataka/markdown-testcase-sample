# Pull Request #10: 打刻修正機能の実装

## 概要

Issue #1 で定義された打刻修正機能を実装。既存の勤怠管理システムに、従業員による打刻の直接修正と修正履歴管理機能を追加。

## 前提：既存システム

既存の勤怠管理システムには以下が実装済み：
- 出退勤打刻機能（`attendance` テーブル、打刻API、打刻画面）
- 打刻履歴表示機能
- 労働時間計算機能

## 変更点

### 1. データベーススキーマの追加

**ファイル**: `db/migrations/002_add_attendance_correction.sql`

- `attendance_corrections` テーブルの追加
  - `id`: 主キー
  - `attendance_id`: 修正対象の打刻ID（外部キー）
  - `employee_id`: 修正者の従業員ID（外部キー）
  - `correction_type`: 修正種別（clock_in, clock_out）
  - `original_time`: 修正前の時刻
  - `corrected_time`: 修正後の時刻
  - `reason`: 修正理由
  - `created_at`, `updated_at`: タイムスタンプ

- `attendance` テーブルへのカラム追加
  - `is_corrected`: 修正フラグ
  - `last_corrected_at`: 最終修正日時

### 2. APIエンドポイントの追加

**ファイル**: `src/api/attendance-correction.js`

- `PUT /api/attendance/:id/correct`: 打刻修正
  - リクエスト: `{ correction_type, corrected_time, reason }`
  - レスポンス: 更新された打刻記録
  - 修正と同時に打刻データを更新し、修正履歴を記録

- `GET /api/attendance/correction/history`: 修正履歴取得
  - クエリパラメータ: `employee_id, start_date, end_date`
  - レスポンス: 修正履歴リスト

- `GET /api/attendance/correction/history/team`: チーム修正履歴取得（上長用）
  - クエリパラメータ: `manager_id, start_date, end_date`
  - レスポンス: 管理下の従業員の修正履歴リスト

### 3. フロントエンド画面の追加

**ファイル**: `src/components/AttendanceCorrectionForm.vue`

- 打刻修正フォーム
  - 修正対象の打刻選択
  - 修正後の時刻入力
  - 修正理由入力（必須）
  - 修正ボタン（即座に反映）

**ファイル**: `src/components/CorrectionHistory.vue`

- 修正履歴表示
  - 修正日時、修正内容、修正理由の表示
  - フィルタ・検索機能
  - 個人用と上長用の2つの表示モード

### 4. 既存機能の修正

**ファイル**: `src/components/AttendanceHistory.vue`

- 修正ボタンの追加
- 修正マークの表示（修正済み打刻に表示）
- 修正履歴の確認機能

### 5. バリデーション追加

**ファイル**: `src/validators/attendance-correction.js`

- 修正時のバリデーション
  - 修正対象の打刻が存在するか確認
  - 修正時刻の妥当性チェック（未来の時刻は不可）
  - 修正可能期間のチェック（3ヶ月以内）
  - 本人の打刻であることの確認

## テスト対象

この変更により、以下をテストする必要がある：

1. **基本動作**: 打刻修正の正常動作
2. **データ整合性**: 修正履歴の正確な記録、打刻データの正確な更新
3. **バリデーション**: 不正な修正の防止、権限チェック
4. **修正履歴表示**: 個人用・上長用の修正履歴表示
5. **既存機能との統合**: 既存の打刻機能への影響がないこと

## 影響範囲

- **新規追加**: 打刻修正機能（新規テーブル、新規API、新規画面）
- **既存機能修正**: 打刻履歴画面に修正ボタンと修正マーク表示を追加
- **関連機能**: 労働時間計算（修正後のデータで再計算）
- **データ**: 新規テーブル追加、既存テーブルへのカラム追加

## デプロイ手順

1. データベースマイグレーション実行
2. APIサーバーのデプロイ
3. フロントエンドのビルド・デプロイ
4. 動作確認
5. 既存ユーザーへの機能説明
