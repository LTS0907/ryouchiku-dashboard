# 経営ダッシュボード（実時管理会計）

このリポジトリは龍竹一生グループの経営管理会計システムです。

---

## システム構成

### 本番環境

| 項目 | URL/情報 |
|------|----------|
| **Webアプリ** | https://ryouchiku-dashboard.vercel.app/ |
| **GitHub** | https://github.com/kazui0907/ryouchiku-dashboard |
| **データベース** | Supabase PostgreSQL（Singapore リージョン） |
| **ホスティング** | Vercel |

---

## データ格納場所

### 1. データベース（Supabase）

**プロジェクト名**: kanri_kaikei
**管理画面**: https://supabase.com/dashboard

| テーブル | 用途 |
|----------|------|
| `MonthlyAccounting` | 月次会計データ（売上・粗利・予算・昨年実績） |
| `AccountingLineItem` | 管理会計明細行（損益計算書の各項目） |
| `WeeklyKPI` | 週次KPI（問合せ件数、商談件数等） |
| `WeeklySiteKPI` | 週次現場KPI |
| `PersonalKPI` | 個人別KPI（従業員ごとの目標・実績） |
| `Budget` | 予算データ |

### 2. ソースデータ（CSVファイル）

Google Drive共有ドライブ内のCSVファイルがデータソースです：
`/ryouchiku-workspace/group/board of directors/`

| ファイル | 用途 |
|----------|------|
| `【共有用】2026度月次会議用管理会計 - 月次予測.csv` | 月次損益計算書（メインデータ） |
| `【共有用】2026度月次会議用管理会計 - 週次KPI（積極版）.csv` | 週次KPI目標・実績 |
| `【共有用】2026度月次会議用管理会計 - 週次現場KPI（積極版）.csv` | 週次現場KPI |
| `【共有用】2026度月次会議用管理会計 - 貸借対照表.csv` | 貸借対照表 |

### 3. アプリケーションコード構成

```
./
├── app/                 # Next.js App Router
│   ├── api/            # APIエンドポイント
│   ├── admin/          # 管理画面
│   └── page.tsx        # ダッシュボードトップ
├── components/         # UIコンポーネント
├── prisma/
│   └── schema.prisma   # データベーススキーマ定義
└── scripts/            # インポートスクリプト
```

---

## データ更新方法

### 方法1: Web管理画面からアップロード
1. https://ryouchiku-dashboard.vercel.app/admin/upload にアクセス
2. 月次予測CSVをアップロード

### 方法2: ローカルスクリプト実行
```bash
npx tsx scripts/import-to-supabase.ts
```

---

## 環境変数

| 変数名 | 説明 |
|--------|------|
| `DATABASE_URL` | Supabase PostgreSQL接続文字列 |

**ローカル**: `.env` ファイル
**本番**: Vercel Settings → Environment Variables

---

## 別のWindowsパソコンでの開発セットアップ手順

### 1. 必要なソフトウェアのインストール

#### Node.js（必須）
1. https://nodejs.org/ にアクセス
2. **LTS版**（推奨）をダウンロードしてインストール
3. インストール確認：
   ```powershell
   node --version
   npm --version
   ```

#### Git（必須）
1. https://git-scm.com/download/win からダウンロード
2. インストール（デフォルト設定でOK）
3. インストール確認：
   ```powershell
   git --version
   ```

#### Visual Studio Code（推奨）
1. https://code.visualstudio.com/ からダウンロード
2. 推奨拡張機能：
   - Prisma
   - ESLint
   - Tailwind CSS IntelliSense

### 2. GitHubからコードを取得

```powershell
# 作業ディレクトリに移動（例：ドキュメント）
cd C:\Users\<ユーザー名>\Documents

# リポジトリをクローン
git clone https://github.com/kazui0907/ryouchiku-dashboard.git

# ディレクトリに移動
cd ryouchiku-dashboard
```

### 3. 環境変数ファイルの作成

プロジェクトフォルダ内に `.env` ファイルを作成：

```
# Supabase PostgreSQL (Singapore region)
DATABASE_URL="postgresql://postgres.hzjtbpwnotuodpkwpnip:4989%21Lifetim@aws-1-ap-southeast-1.pooler.supabase.com:5432/postgres"
```

**注意**: パスワードの `!` は `%21` にURLエンコードされています

### 4. 依存パッケージのインストール

```powershell
npm install
```

### 5. Prismaクライアントの生成

```powershell
npx prisma generate
```

### 6. 開発サーバーの起動

```powershell
npm run dev
```

ブラウザで http://localhost:3000 にアクセスして動作確認

### 7. コードを変更してデプロイ

```powershell
# 変更をステージング
git add .

# コミット
git commit -m "変更内容の説明"

# GitHubにプッシュ（自動でVercelにデプロイされる）
git push origin main
```

---

## トラブルシューティング

### npm install でエラーが出る場合
```powershell
# キャッシュをクリアして再試行
npm cache clean --force
Remove-Item -Recurse -Force node_modules
npm install
```

### Prisma関連のエラー
```powershell
# Prismaクライアントを再生成
npx prisma generate
```

### データベース接続エラー
- `.env` ファイルの `DATABASE_URL` が正しいか確認
- インターネット接続を確認
- Supabaseプロジェクトがアクティブか確認（https://supabase.com/dashboard）

### ポート3000が使用中の場合
```powershell
# 別のポートで起動
npm run dev -- -p 3001
```

---

## 注意事項

- データベースのパスワードには特殊文字（`!`）が含まれているため、URLエンコードされています
- CSVファイルを更新した場合は、Webアップロードまたはスクリプトでデータベースに反映してください
- GitHubにプッシュすると、Vercelが自動デプロイします
- `.env` ファイルはGitにコミットしないでください（`.gitignore`で除外済み）
