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
- **Discord通知**：モデルの追加・変更があった場合のみ通知

## ファイル構成

```
.
├── .github/
│   └── workflows/
│       └── update-gemini-models.yml  # GitHub Actionsワークフロー
├── models.json                        # 取得したモデル一覧（自動更新）
└── README.md
```

## License

MIT
