# Circulation Enterprise Dashboard — CLAUDE.md

## プロジェクト概要
サーキュレーション社エンタープライズ部門のアカウントプラン（N Multiple MODEL）ダッシュボード。
Salesforce → G-Connector → Google Sheets → GAS doGet → 単一HTML(GitHub Pages) のパイプラインでリアルタイム可視化。

**所有者:** 徳永 雄大（エンタープライズMgr）
**チームメンバー:** 徳永、中村春香、岡部瑛美、栫英輝、福本悠太（この5名のみ表示）
**デプロイ:** GitHub Pages https://tokunagayudai.github.io/Circulation-Enterprise-dashboard/
**リポジトリ:** https://github.com/tokunagayudai/Circulation-Enterprise-dashboard

## アーキテクチャ
```
Salesforce (3レポート)
  ↓ G-Connector (Chrome拡張)
Google Sheets (1_TJRjTq8PRg0FTs36CbB5I45EKrccVEkpqWw2UO7Stc)
  ↓ GAS doGet API
index.html (GitHub Pages / 単一ファイル)
  ├── 埋め込みJSON (フォールバック用)
  └── GAS fetch (LIVE時はこちら優先)
```

## GAS URL
```
https://script.google.com/macros/s/AKfycbwuB-bko02MczlRorWD0qQ7Ch80fLoZd5Gmtwb2BWKDfOLOlOBokjiBNjodupe-r2VF/exec
```
パラメータ: `?sheet=シート名` でCSV返却

## スプレッドシート6シート構成
| シート名 | 用途 | 備考 |
|----------|------|------|
| funnel | 商況ファネル分析 | 将来マージ用 |
| juchu | 商況受注管理 | 将来マージ用 |
| pn | 商況稼働案件 | 将来マージ用 |
| acplan_cases | アカプラ案件 | 現在使用中 |
| acplan_contacts | アカプラリード | 現在使用中 |
| acplan_pn | アカプラPN（稼働案件） | 現在使用中 |

## CSVヘッダー（確定・ハードコード済み）

### acplan_cases（列番号→フィールド）
```
0: 主担当者
1: 取引先名
2: 案件名
3: 顧客担当者氏名
4: CK
5: フェーズ
6: 最終到達フェーズ
7: 受注予定日・受注日
8: 契約開始日
9: 契約終了日
10: 新規/継続
11: 種別
12: 作成日
13: 案件 ID
```

### acplan_contacts（列番号→フィールド）
```
0: 取引先名
1: 所属部署
2: 名
3: 姓
4: 役職
5: 電話
6: メール
7: 取引先 所有者
8: 作成者
9: 作成日
10: 取引先 ID
11-14: 案件数系
15: 最終活動日
16: ターゲット/カテゴリ
```

### acplan_pn（列番号→フィールド）
```
0: 主担当者
1: 取引先名
2: 案件名
3: フェーズ
4: 最終到達フェーズ
5: CK
6: 受注予定日・受注日
7: 作成日
8: 種別
9: 案件 ID
10: [月額]顧客請求額(税抜)
11: [月額]専門家支払額(税抜)
12: 契約開始日
13: 契約終了日
14: [総額]顧客請求額
15: 総支払額(税抜)
16: 取引先 ID
17: 案件レコードタイプ
18: 案件レコードタイプ名
19: 解約承認日
20: 解約時マイナスCK額
21: 解約日
```

## SFレポートID
| レポート | ID |
|----------|-----|
| 案件（純粋版） | 00Odc00000KuvL3EAJ |
| 受注管理 | 00Odc00000MPjgPEAT |
| 稼働案件 | 00Odc00000Lf7XhEAJ |

## Salesforce URL
```
SF Base: https://circulation.lightning.force.com/lightning/r/
Account: {base}Account/{id}/view
Opportunity: {base}Opportunity/{id}/view
```

## 画面構成
**サイドバー（2タブ）:**
- 📊 総合 — KPI + 売上チャート + 企業カード一覧
- 🏢 企業別 — 企業詳細 + 組織マップ

### 総合ダッシュボード
- 楽天証券風KPIカード: FY13累計 + デルタ切替（先週比/今月/先月比/下期）
- 担当者フィルター（全担当者 / 個人）
- KPIドリルダウン: カードクリック→リード/案件一覧
- Tier1 月別売上推移: デュアルバー（青=売上、緑=粗利、%=粗利率）
- 売上ドリルダウン: 月クリック→企業集約→企業クリック→案件展開（2段階）
- 企業カードグリッド: マネフォ風5列

### 企業別ページ
- KPIカード5枚（新規リード/商談P1+/案件P2+/面談P4+/受注P8）
- サマリー行（全リード/全部署/累計受注/CK/売上/粗利）
- 過去受注セクション（受注済リード/部署/案件 ドリルダウン）
- 月別売上/粗利チャート
- AI組織図解析（Claude API: claude-sonnet-4-20250514）
- 組織マップ（Pathlight風ネストカード）

## 組織マップ機能
- **ドラッグ&ドロップ:** 部署の階層化 + リード個別移動（stopPropagation）
- **部署抽出:** 子階層→トップへ戻す（↗ボタン）
- **＋部署追加:** ホワイトスペース部署の手動作成
- **＋担当者:** ホワイトスペースリードの手動作成（WSバッジ）
- **WS編集/削除:** ✏️編集 / 🗑削除
- **☑チェックボックス:** アプローチ対象フラグ（localStorage永続化）
- **フィルター:** すべて / 稼働中 / 未進展 / ☑アプローチ対象
- **Notion紐付:** 企業ごとにNotionページURLを保存（localStorageに永続化）
- **ツリーリセット:** localStorageのカスタムツリーをクリア
- **キーマンバッジ:** 決裁者（紫）/ チャンピオン（青）/ キーマン（橙）カタカナ表記

## ステータス定義
| status | 条件 | カラー |
|--------|------|--------|
| won | フェーズ=08 | 緑 |
| meeting | フェーズ≥04 | 橙 |
| deal | フェーズ≥02 | 青 |
| prospect | フェーズ≥01 | 黄 |
| contacted | 案件あり（全DEAD） | グレー |
| dead | DEAD-* | 赤 |
| registered | 案件なし | 無色 |
| whitespace | 手動追加 | 紫 |

## 名前マッチング
G-Connectorは姓名を逆順で返すことがある。
contacts列2=名、列3=姓 → `(姓+名)` と `(名+姓)` の両方向 + 空白除去でマッチング。

## PNデータ処理ルール
1. フェーズ=08-Closedのみ計上（DEADは除外）
2. 月額請求=¥0でも専門家支払>¥0ならPN人数としてカウント
3. 解約日（列21）があれば、解約日以降の月は除外
4. FY期間: 2025/08 ～ 2026/09

## デザインルール
- ブランドカラー: #0079C0
- フォント: -apple-system, 'Noto Sans JP', sans-serif
- 数値フォント: 'SF Mono', Consolas, monospace
- 背景: #f6f8fa（ライトグレー）
- Clearbitロゴ: 廃止済み（サービス停止）

## localStorage使用
| キー | 用途 |
|------|------|
| acplan_tree_{企業名} | 組織マップのカスタムツリー |
| acplan_notion_{企業名} | 企業ごとのNotionページURL |
| acplan_checked_{企業名} | ☑チェック済みリード |

## Notion連携
- アカプラ一覧: https://www.notion.so/2d35b464b52681e598bdc625c85f8179?v=2d35b464b52681d590ad000c86bc4e67
- 三菱電機: https://www.notion.so/2e85b464b52681ad86b6fc2dc031e5c6
- 各企業のNotionページURLはダッシュボード内「🔗 Notion紐付」ボタンで設定

## 将来のマージ予定
商況ダッシュボード（enterprise_dashboard_v4）を同じHTMLに統合予定。
統合後サイドバー: 📊ダッシュボード / 👥メンバー分析 / 💰売上CK / 🏢アカプラ / 📋経営報告 / ⚙️設定

## 開発ワークフロー
```bash
# 1. リポジトリをクローン
git clone https://github.com/tokunagayudai/Circulation-Enterprise-dashboard.git
cd Circulation-Enterprise-dashboard

# 2. 編集
# index.html を直接編集

# 3. ローカルテスト（GASフェッチはCORS制限で動かない→埋め込みデータで確認）
# python3 -m http.server 8080 でローカルサーバー起動
# ※ file:// だとGASフェッチスキップ、http://localhost ならGAS試行（CORS次第）

# 4. デプロイ
git add -A && git commit -m "fix: description" && git push
# GitHub Pages自動反映（1-2分）

# 5. 本番確認
# https://tokunagayudai.github.io/Circulation-Enterprise-dashboard/
# Console: Team filter: 23 companies / PN: included=458
```

## 注意事項
- 単一HTMLファイル（327KB）。CSS/JSはインライン。
- 埋め込みJSON（var R={...}）はフォールバック用。GASデータが優先。
- GASフェッチ成功時は右上に「✅ LIVE」、失敗時は「📦 オフライン」バッジ表示。
- チームフィルター: 主担当者に徳永/中村/岡部/栫/福本が含まれる企業のみ。
- FS（担当者名）はcasesの列0から取得（contactsの列7はメールアドレスのため不使用）。
