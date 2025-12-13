# Pull Request #10: 打刻修正機能の実装

## 概要

Issue #1 で定義された打刻修正機能を実装。既存の勤怠管理システムに、従業員による打刻修正申請と上長による承認機能を追加。

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
  - `employee_id`: 申請者の従業員ID（外部キー）
  - `correction_type`: 修正種別（clock_in, clock_out）
  - `original_time`: 修正前の時刻
  - `corrected_time`: 修正後の時刻
  - `reason`: 修正理由
  - `status`: ステータス（pending, approved, rejected）
  - `approved_by`: 承認者ID
  - `approved_at`: 承認日時
  - `approval_comment`: 承認コメント
  - `created_at`, `updated_at`: タイムスタンプ

- `attendance` テーブルへのカラム追加
  - `has_correction`: 修正履歴の有無フラグ
  - `correction_count`: 修正回数

### 2. APIエンドポイントの追加

**ファイル**: `src/api/attendance-correction.js`

- `POST /api/attendance/correction`: 打刻修正申請
  - リクエスト: `{ attendance_id, correction_type, corrected_time, reason }`
  - レスポンス: 修正申請記録
  - 申請時に上長に通知を送信

- `GET /api/attendance/correction/pending`: 承認待ち修正申請一覧取得
  - クエリパラメータ: `approver_id`
  - レスポンス: 承認待ちの修正申請リスト

- `PUT /api/attendance/correction/:id/approve`: 修正申請の承認
  - リクエスト: `{ approval_comment }`
  - レスポンス: 承認済み修正申請記録
  - 承認時に打刻データを自動更新

- `PUT /api/attendance/correction/:id/reject`: 修正申請の却下
  - リクエスト: `{ rejection_reason }`
  - レスポンス: 却下済み修正申請記録

- `GET /api/attendance/correction/history`: 修正履歴取得
  - クエリパラメータ: `employee_id, start_date, end_date`
  - レスポンス: 修正履歴リスト

### 3. フロントエンド画面の追加

**ファイル**: `src/components/AttendanceCorrectionForm.vue`

- 打刻修正申請フォーム
  - 修正対象の打刻選択
  - 修正後の時刻入力
  - 修正理由入力（必須）
  - 申請ボタン

**ファイル**: `src/components/CorrectionApprovalList.vue`

- 承認待ち修正申請一覧
  - 申請者、申請日、修正内容の表示
  - 承認・却下ボタン
  - 詳細表示機能

**ファイル**: `src/components/CorrectionHistory.vue`

- 修正履歴表示
  - 修正日時、修正内容、承認者の表示
  - フィルタ・検索機能

### 4. 既存機能の修正

**ファイル**: `src/components/AttendanceHistory.vue`

- 修正申請ボタンの追加
- 修正履歴の表示追加（修正マークの表示）

### 5. バリデーション追加

**ファイル**: `src/validators/attendance-correction.js`

- 修正申請時のバリデーション
  - 修正対象の打刻が存在するか確認
  - 修正時刻の妥当性チェック（未来の時刻は不可）
  - 修正可能期間のチェック（3ヶ月以内）
  - 重複申請のチェック

- 承認時のバリデーション
  - 承認者の権限チェック
  - 申請が承認待ち状態か確認

### 6. 通知機能の追加

**ファイル**: `src/services/notification.js`

- 修正申請通知（上長へ）
- 承認通知（申請者へ）
- 却下通知（申請者へ）

## テスト対象

この変更により、以下をテストする必要がある：

1. **基本動作**: 修正申請・承認の正常動作
2. **データ整合性**: 修正履歴の正確な記録、打刻データの正確な更新
3. **バリデーション**: 不正な修正申請の防止、権限チェック
4. **承認フロー**: 申請から承認までのフロー、却下処理
5. **通知機能**: 各種通知の正確な配信
6. **既存機能との統合**: 既存の打刻機能への影響がないこと

## 影響範囲

- **新規追加**: 打刻修正機能（新規テーブル、新規API、新規画面）
- **既存機能修正**: 打刻履歴画面に修正申請ボタンと修正履歴表示を追加
- **関連機能**: 労働時間計算（修正後のデータで再計算）
- **データ**: 新規テーブル追加、既存テーブルへのカラム追加

## デプロイ手順

1. データベースマイグレーション実行
2. APIサーバーのデプロイ
3. フロントエンドのビルド・デプロイ
4. 動作確認
5. 既存ユーザーへの機能説明
