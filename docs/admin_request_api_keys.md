# API利用申請：商談自動化パイプライン構築のため

## 申請者
徳永 雄大（エンタープライズMgr）

## 目的
Zoom商談録画から、AIが自動で要件整理・SF入力を行うパイプラインを構築します。
FSの手動入力工数を削減し、案件情報の鮮度と品質を担保します。

## 必要なもの

### 1. Anthropic（Claude）APIキー
- 用途：Zoom文字起こしテキストをClaude AIで解析し、案件要件整理・フェーズ判定を自動化
- 取得先：https://console.anthropic.com → API Keys → Create Key
- 必要な権限：APIキーの作成権限
- コスト目安：月間50〜100商談の処理で月$50〜100程度（従量課金）
- モデル：claude-sonnet-4-20250514（コスト効率が良い）

### 2. Zoom Server-to-Server OAuthアプリの作成許可
- 用途：Zoom録画の文字起こしデータを自動取得
- 取得先：https://marketplace.zoom.us → Develop → Build App → サーバー間OAuthアプリ
- 必要なスコープ：
  - `cloud_recording:read:admin`（録画データの読み取り）
  - `user:read:admin`（ユーザー情報の読み取り）
- セキュリティ：Server-to-Server OAuthはアカウントレベルの認証で、個人のログイン情報は不要

## セキュリティ上の配慮
- APIキーはGAS（Google Apps Script）のスクリプトプロパティに保存（コードにハードコードしない）
- Zoom録画データは処理後に破棄（永続保存しない）
- Claude APIへの送信データは商談の文字起こしテキストのみ
- Anthropicのデータポリシー：API経由のデータはモデルトレーニングに使用されない

## 期待される効果
- FS1人あたり月2〜3時間の入力工数削減
- SF案件情報の入力率100%（現状は未入力・遅延が多い）
- フェーズ判定の標準化（FSの主観に依存しない）
- マネージャーの案件把握スピード向上（商談後即座に確認可能）
