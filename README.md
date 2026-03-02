# Gemini Models Tracker

Google Gemini APIで利用可能なモデル一覧を毎日自動取得・追跡するリポジトリです。

## 概要

毎日UTC 0:00にGitHub Actionsが自動でGemini APIからモデル一覧を取得し、変更があった場合にコミット・プッシュします。  
モデルの追加・変更があった場合はDiscordに通知されます。

## セットアップ

### 必要なSecrets

リポジトリの **Settings** → **Secrets and variables** → **Actions** に以下を登録してください。

| Secret名              | 内容                              |
| --------------------- | --------------------------------- |
| `GEMINI_API_KEY`      | Google AI StudioのAPIキー         |
| `DISCORD_WEBHOOK_URL` | Discord通知用WebhookのURL（任意） |

### APIキーの取得

- **Gemini API Key**：[Google AI Studio](https://aistudio.google.com/app/apikey) から取得
- **Discord Webhook URL**：Discordのチャンネル設定 → 連携サービス → ウェブフックから取得

## 動作

- **実行タイミング**：毎日 UTC 0:00（日本時間 9:00）
- **手動実行**：Actionsタブから `workflow_dispatch` で任意実行可能
- **変更検知**：`models.json` に差分がある場合のみコミット・プッシュ
- **Discord通知**：モデルの追加・変更があった場合に変更されたモデル名を含む通知を送信
  - `+ models/...`（緑）：新しく追加されたモデル
  - `- models/...`（赤）：削除されたモデル

### 注意事項

- GitHub Actionsのスケジュール実行（`schedule`）は遅延が発生する場合があります
- より正確な定時実行が必要な場合は、外部cronサービスから `workflow_dispatch` をトリガーする方法もあります

### 外部cronサービスを使う方法（任意）

[cron-job.org](https://cron-job.org) 等の外部サービスからワークフローを定時実行できます。

#### 1. GitHub Personal Access Token を作成

1. [GitHub Settings → Fine-grained Tokens](https://github.com/settings/tokens?type=beta) にアクセス
2. 対象リポジトリを選択し、**Actions** の権限を **Read and write** に設定
3. トークンを生成してコピー

#### 2. cron-job.org の設定

| 項目     | 値                                                                                                  |
| -------- | --------------------------------------------------------------------------------------------------- |
| URL      | `https://api.github.com/repos/{owner}/{repo}/actions/workflows/update-gemini-models.yml/dispatches` |
| Method   | `POST`                                                                                              |
| Headers  | `Authorization: Bearer {YOUR_TOKEN}`                                                                |
| Headers  | `Accept: application/vnd.github+v3+json`                                                            |
| Headers  | `Content-Type: application/json`                                                                    |
| Body     | `{"ref":"main"}`                                                                                    |
| Schedule | 任意の時刻（例：毎日 JST 9:00）                                                                     |

> レスポンスが `204 No Content` であれば成功です。

## CI / 依存関係の管理

### CI（継続的インテグレーション）

PRが作成されると `.github/workflows/ci.yml` が自動実行されます。

| Job                       | 内容                                                            | 実行タイミング                |
| ------------------------- | --------------------------------------------------------------- | ----------------------------- |
| **Job 1: ロジックテスト** | diffロジック・JSONペイロードをフィクスチャで検証（Secrets不要） | PR作成時・自動実行            |
| **Job 2: APIテスト**      | Gemini API接続・JSONバリデーション・Discord通知の実送信         | 手動実行（workflow_dispatch） |

> DependabotのPRが来た場合、Job 1は自動実行されます。Job 2はActionsタブの「Run workflow」から手動実行してください。

### Dependabot

`.github/dependabot.yml` により、GitHub Actionsの依存アクション（例：`actions/checkout`）のバージョンアップが**毎週自動でPR作成**されます。

- CIが通ることを確認してからマージしてください

## ファイル構成

```
.
├── .github/
│   ├── dependabot.yml                 # Dependabot設定（依存関係の自動更新）
│   └── workflows/
│       ├── update-gemini-models.yml   # GitHub Actionsワークフロー（本番）
│       └── ci.yml                     # CIワークフロー（PR検証）
├── models.json                        # 取得したモデル一覧（自動更新）
└── README.md
```

## License

MIT

---

# Gemini Models Tracker (English)

A repository that automatically fetches and tracks the list of available models from the Google Gemini API every day.

## Overview

GitHub Actions runs daily at UTC 0:00, fetches the model list from the Gemini API, and commits/pushes if any changes are detected.  
A Discord notification is sent when models are added or updated.

## Setup

### Required Secrets

Register the following in your repository's **Settings** → **Secrets and variables** → **Actions**.

| Secret Name           | Description                                      |
| --------------------- | ------------------------------------------------ |
| `GEMINI_API_KEY`      | Your Google AI Studio API key                    |
| `DISCORD_WEBHOOK_URL` | Discord Webhook URL for notifications (optional) |

### Getting API Keys

- **Gemini API Key**: Obtain from [Google AI Studio](https://aistudio.google.com/app/apikey)
- **Discord Webhook URL**: Channel Settings → Integrations → Webhooks

## How It Works

- **Schedule**: Daily at UTC 0:00 (JST 9:00)
- **Manual trigger**: Can be run manually via `workflow_dispatch` in the Actions tab
- **Change detection**: Only commits and pushes when `models.json` has changes
- **Discord notification**: Sends a notification including the changed model names when models are added or updated
  - `+ models/...` (green): Newly added models
  - `- models/...` (red): Removed models

### Notes

- GitHub Actions schedule (`schedule`) may have delays
- For more precise scheduling, you can trigger `workflow_dispatch` from an external cron service

### Using an External Cron Service (Optional)

You can use external services like [cron-job.org](https://cron-job.org) to trigger the workflow on a precise schedule.

#### 1. Create a GitHub Personal Access Token

1. Go to [GitHub Settings → Fine-grained Tokens](https://github.com/settings/tokens?type=beta)
2. Select the target repository and set **Actions** permission to **Read and write**
3. Generate and copy the token

#### 2. Configure cron-job.org

| Setting  | Value                                                                                               |
| -------- | --------------------------------------------------------------------------------------------------- |
| URL      | `https://api.github.com/repos/{owner}/{repo}/actions/workflows/update-gemini-models.yml/dispatches` |
| Method   | `POST`                                                                                              |
| Headers  | `Authorization: Bearer {YOUR_TOKEN}`                                                                |
| Headers  | `Accept: application/vnd.github+v3+json`                                                            |
| Headers  | `Content-Type: application/json`                                                                    |
| Body     | `{"ref":"main"}`                                                                                    |
| Schedule | Any time (e.g., daily at 9:00 AM JST)                                                               |

> A `204 No Content` response indicates success.

## CI / Dependency Management

### CI (Continuous Integration)

When a PR is opened, `.github/workflows/ci.yml` runs automatically.

| Job                   | Description                                                                   | Trigger                    |
| --------------------- | ----------------------------------------------------------------------------- | -------------------------- |
| **Job 1: Logic Test** | Validates diff logic and Discord payload using fixtures (no Secrets required) | Automatic on PR            |
| **Job 2: API Test**   | Gemini API connection, JSON validation, and actual Discord notification send  | Manual (workflow_dispatch) |

> For Dependabot PRs, Job 1 runs automatically. Run Job 2 manually from the Actions tab → "Run workflow" before merging.

### Dependabot

`.github/dependabot.yml` automatically creates weekly PRs to update GitHub Actions dependencies (e.g., `actions/checkout`).

- Always confirm CI passes before merging

## File Structure

```
.
├── .github/
│   ├── dependabot.yml                 # Dependabot config (auto dependency updates)
│   └── workflows/
│       ├── update-gemini-models.yml   # GitHub Actions workflow (production)
│       └── ci.yml                     # CI workflow (PR validation)
├── models.json                        # Fetched model list (auto-updated)
└── README.md
```

## License

MIT
