# Sharks Score App

少年サッカーチームの試合記録・スコア管理 Web アプリ。

保護者・コーチがスマホから試合中にリアルタイムでスコアを入力し、  
選手スタッツの集計・Gemini AI による試合分析までをワンストップで管理できます。  
Vercel にデプロイし、本番稼働中。

---

## 主な機能

| 機能 | 概要 |
|---|---|
| リアルタイムスコア入力 | 試合中に得点・得点者・MVPをスマホから即時入力 |
| 学年別管理 | U12 / U11 など複数カテゴリーを独立管理 |
| 得点ランキング | 全試合集計から自動算出するトップスコアラー表示 |
| Gemini AI 試合分析 | 蓄積された試合データを AI が分析しフィードバックを生成 |
| アーカイブ | 完了試合の履歴閲覧・フィルタリング |
| 設定管理 | チーム名・カラー・学年ごとのスプレッドシートIDをUI上で管理 |

---

## 技術スタック

| カテゴリ | 採用技術 |
|---|---|
| フレームワーク | Next.js 16（App Router） |
| UI | React 19 / Tailwind CSS v4 / Lucide React |
| 言語 | TypeScript |
| 認証 | NextAuth v4（Google OAuth） |
| データストア | Google Spreadsheet API（サーバーレスDBとして活用） |
| AI | Gemini API（`@google/generative-ai`） |
| デプロイ | Vercel |

---

## アーキテクチャ

```
ブラウザ（スマホ）
  │
  ├── NextAuth（Google認証）
  │
  ├── Next.js App Router（API Routes）
  │   ├── /api/matches        → 試合CRUD
  │   ├── /api/matches/analyze → Gemini AI分析
  │   ├── /api/grades         → 学年管理
  │   └── /api/masters        → 選手・会場マスタ
  │
  └── Google Spreadsheet（データストア）
        ├── 試合データシート（学年別）
        └── 共通マスタシート（選手・会場）
```

データベースをスプレッドシートで代替することで、コーチ・保護者がデータを直接確認・修正できる運用を実現しています。

---

## セットアップ

### 前提条件

- Node.js 20 以上
- Google Cloud プロジェクト（OAuth クライアント + Sheets API 有効化）
- Google Spreadsheet（試合データ保存用）
- Gemini API キー

### インストール

```bash
git clone https://github.com/sharks-ark2023-jpg/sharks-score-app.git
cd sharks-score-app
npm install
```

### 環境変数

`.env.local` を作成し、以下を設定：

```env
# NextAuth
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_secret

# Google OAuth
GOOGLE_CLIENT_ID=your_client_id
GOOGLE_CLIENT_SECRET=your_client_secret

# Google Sheets（サービスアカウント）
GOOGLE_SERVICE_ACCOUNT_EMAIL=your_sa@project.iam.gserviceaccount.com
GOOGLE_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\n..."

# 学年とスプレッドシートIDのマッピング
GRADES_CONFIG=U12:spreadsheet_id_1,U11:spreadsheet_id_2

# Gemini AI
GEMINI_API_KEY=your_gemini_api_key
```

### 開発サーバー起動

```bash
npm run dev
```

[http://localhost:3000](http://localhost:3000) で確認できます。

---

## 設計上の工夫

**スプレッドシートをDBとして使う設計**  
サービスアカウント経由で Google Spreadsheet を直接読み書きします。チームの運営者が技術知識なしにデータを確認・修正できることを優先した選択です。

**LINE 内ブラウザ対策**  
チームの連絡はLINEが中心のため、LINE 内ブラウザ（認証が通らない）を検出した場合に外部ブラウザへ誘導するリダイレクトを実装しています。

**iPhone SafeArea 対応**  
現場利用を想定してフッターナビゲーションを採用し、iPhone X 以降のノッチ・ホームバーへの対応を徹底しています。

---

## 開発者

荒木 譲介（個人開発）  
設計・実装・デプロイまで一人で担当。Claude Code・Antigravity と協働で開発。
