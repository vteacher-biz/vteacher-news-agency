---
title: "俯瞰していた人が重い腰を上げてやっと始めるMCP入門" # 記事のタイトル
emoji: "💻" # アイキャッチとして使われる絵文字（1文字だけ）
type: "idea" # tech: 技術記事 / idea: アイデア記事
topics: ["ai", "mcp"] # タグ。["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

# まずはMCPのイメージ図を10秒間見つめる

パソコンにUSBで接続することで、外付けのさまざまなハードウェアを利用できるようになることを、MCPでイメージしたもの。

https://x.com/norahsakal/status/1898183864570593663


# 必要なもの（0分〜）

次の中からAIエージェントをひとつ選んでください。
（ひとつも環境がなければ、インストールしておいてください）

|  | AIエージェント | AIエディタ |
| ---- | ---- | ---- |
| 1 | Copilot  (Agent mode) | [VS Code](https://code.visualstudio.com/)  ※v1.99以降 |
| 2 | Cline | [VS Code](https://code.visualstudio.com/) + [Cline](https://github.com/cline/cline) |
| 3 | Roo code | [VS Code](https://code.visualstudio.com/) + [Roo code]( https://docs.roocode.com/getting-started/installing) |
| 4 | Cursor | [Cursor](https://www.cursor.com/ja) |
| 5 | Claude (Desktop) | [Claude Desktop](https://claude.ai/download) |

なお、説明用にDocker版を使用するため、あらかじめDockerをインストールしておいてください。
https://www.docker.com/ja-jp/


# 目次：体験してみること

MCPサーバーが無いとできないことを体験してみましょう。

1. **AIエディタをSlackと連携してみましょう**

現段階（標準機能のみ）では、
たとえば Cursor に「この回答を Slack に投稿して」とお願いしても、対応してくれません。

3. **AIエディタをGitHubと連携してみましょう**

現段階（標準機能のみ）では、
たとえば Cline に「このリポジトリの Issue 一覧を教えて」と尋ねても、答えることはできません。


# はじめに：MCPサーバーの記述方法（10秒）

次のような JSON ファイルを記述することで、容易に MCP サーバーを追加できます。

```json
{
  "mcpServers": {
    // ------------------------------ ↓ここから
    "＜MCPサーバーの名前＞": {
      "command": "＜コマンド＞",
      "args": [
        "＜オプション1＞",
        "＜オプション2＞",
      ],
      "env": {
        "＜環境変数1＞": "＜APIキーなど＞",
        "＜環境変数2＞": "＜APIキーなど＞",
      }
    }
    // ------------------------------ ↑ここまでが、1つのMCPサーバーの定義
  }
}
```

ここには起動するためのコマンドが書かれています。

```sh
# GitHubのMCPサーバーの例:
docker  run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN mcp/github
^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
コマンド                        オプション
                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                               環境変数
```


# SlackのMCPサーバーをインストールしてみる（5分）

Slack MCPサーバー の JSON です。公式は [ここ](https://github.com/modelcontextprotocol/servers/tree/main/src/slack) です。環境が汚れないのでDocker版を使うことにします。

（JSONは他のMCPサーバーと併用可能ですので、追記する形で問題ありません）

```json
{
  "mcpServers": {
    // ------------------------------ ↓ここからコピー
    "slack": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "SLACK_BOT_TOKEN",
        "-e",
        "SLACK_TEAM_ID",
        "-e",
        "SLACK_CHANNEL_IDS",
        "mcp/slack"
      ],
      "env": {
        "SLACK_BOT_TOKEN": "<SLACK_BOT_TOKEN>",
        "SLACK_TEAM_ID": "<SLACK_TEAM_ID>",
        "SLACK_CHANNEL_IDS": "<SLACK_CHANNEL_IDS>"
      }
    }
    // ------------------------------ ↑ここまでをコピー
  }
}
```

:::details 補足：Slackのキー<SLACK_BOT_TOKEN>,<SLACK_TEAM_ID>,<SLACK_CHANNEL_IDS>の取得方法
**環境変数の値**
Slack App の設定が初めての場合は、ここから作成しておきます。

- https://api.slack.com/apps

`SLACK_BOT_TOKEN` は `OAuth & Permissions` の `Bot User OAuth Token` に記載されてる値です。 `Bot Token Scopes` には `chat:write` などを付与しておきましょう。

SLACK_TEAM_ID と SLACK_CHANNEL_IDS は Slack の URL からわかります。
該当チャンネルを開いたときの URL を参照してください。

```ts
https://app.slack.com/client/{SLACK_TEAM_ID}/{SLACK_CHANNEL_IDS}
```
:::

### JSON ファイルの場所

MCP サーバ用の JSON ファイルの場所です。これに追記します。

| AIエージェント | jsonの場所 | 手順 |
| ---- | ---- | ---- |
| Copilot (Agent mode) | settings.json | VSCode 👉 Copilotアイコン 👉 チャットを開く 👉 「MCP Servers」アイコン 👉 構成の表示 👉 settings.json 👉 Start（起動）をクリック |
| Cline | cline_mcp_settings.json | VSCode 👉 Cline 👉 「MCP Servers」アイコン 👉 Installed 👉 Configure MCP Servers 👉 cline_mcp_settings.json 👉 インストールしたMCPサーバーをON |
| Roo Code | .roo/mcp.json | VSCode 👉 Roo Code 👉 「MCP Servers」アイコン 👉 MCPを編集 👉 .roo/mcp.json 👉 インストールしたMCPサーバーをON |
| Cursor | .cursor/mcp.json | Curor 👉 Preferences 👉 Curor Settings 👉 MCP 👉 MCP Servers 👉 .cursor/mcp.json 👉 MCP Servers に戻ってON |
| Claude Desktop | claude_desktop_config.json | Claude Desktop 👉 設定 👉 開発者 👉 構成を編集 👉 claude_desktop_config.json 👉 Claude Desktopを再起動 |

- **Dockerが起動してることを確認してください。**
- **同じMCPサーバーが多重起動になっている場合、一旦全てを停止後に開始してください。**

### AIエージェントにSlack投稿を依頼する

AIエージェントとの対話を開始します！
（Copilot や Curor は `Agent` モードにしてください）

「**今日の日付をSlackに投稿して**」と聞いてみてください。

![](https://storage.googleapis.com/zenn-user-upload/07e837fe2d0a-20250506.png)

登録できると思います！

![](https://storage.googleapis.com/zenn-user-upload/ff088bbfd900-20250506.png)


# GitHubのMCPサーバーをインストールしてみる（5分）

こちらもDocker版が用意されているので、こちらでやります。

（JSONは他のMCPサーバーと併用可能ですので、追記する形で問題ありません）

- ~~[公式] https://github.com/modelcontextprotocol/servers/tree/main/src/github~~
- [公式] http://github.com/github/github-mcp-server

```json
{
  "mcpServers": {
    // ------------------------------ ↓ここからコピー
    "github": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "mcp/github"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<YOUR_TOKEN>"
      }
    }
    // ------------------------------ ↑ここまでをコピー
  }
}
```

:::details 補足：GitHubのキー<YOUR_TOKEN>の取得方法
**環境変数の値**
GitHub GITHUB_PERSONAL_ACCESS_TOKEN は、ここから取得できます。

自身のGitHubにログイン後、

- Fine-grained personal access tokens
https://github.com/settings/personal-access-tokens

ここで `Generate new token`して、Repository access と Permissions を設定して、tokenを取得してください。
:::

### JSON ファイルの場所

※ Slackの際の「JSONファイルの場所」を参照してください。

### AIエージェントにIssueの一覧を聞く

AIエージェントとの対話を開始します！
（Copilot や Curor は `Agent` モードにしてください）

「**このリポジトリのIssue一覧を教えて**」と聞いてみてください。

一覧が表示されると思います！

![](https://storage.googleapis.com/zenn-user-upload/0d5376024c7b-20250506.png)


# 自作のMCPサーバーを導入してみる（10分）

MCPサーバーを自作することも可能です。  
社内プロジェクトに特化した関数を柔軟に作成できます。

さまざまな方法がありますが、ここでは Fastmcp というフレームワークを使ってみます。  
以下のリポジトリからサンプルコードを取得してください。

https://github.com/rgbkids/fastmcp-example

- 手順

```sh
# リポジトリをcloneして、
git clone https://github.com/rgbkids/fastmcp-example

# installしてください
npm i
```

### コードをみる

`src/index.ts` に独自のMCPサーバー用関数が書かれています。

```ts
function myMCPServer(text: string) {
  return `${text} by myMCPServer`
}
```

※ ちなみにこのexampleでは、ただ、渡された文字列をそのまま返しているだけです。

名称はここで定義されています。

```ts
server.addTool({
  name: "myMCPServer",
  description: "Returns the input text as-is",
  ...
```

この `name` と `description` は、LLM（大規模言語モデル）の視点から見て理解しやすいように記述する必要があります。AIエディタはこれらの情報を基に、このMCPサーバーがどのような機能を提供するものかを判断します。

### 自作のMCPサーバーを起動する

JSON はこのようになります。

```ts
{
  "mcpServers": {
    // ------------------------------ ↓ここからコピー
    "my-mcp-server-example": {
      "command": "＜nodeフルパス＞/node",
      "args": [
        "＜repoフルパス＞/fastmcp-example/node_modules/tsx/dist/cli.mjs",
        "＜repoフルパス＞/fastmcp-example/src/index.ts"
      ],
      "disabled": false,
      "alwaysAllow": []
    }
    // ------------------------------ ↑ここまでをコピー
  }
}
```

- `src/index.ts` を直接指定しています。
- ＜nodeフルパス＞は node までのパスを指定してください。
  ※ ターミナルから `which node` で通常はわかります。
- ＜repoフルパス＞はリポジトリを clone した場所を指定してください。

### JSON ファイルの場所

※ Slackの際の「JSONファイルの場所」を参照してください。

### AIエージェントに聞いてみる

AIエージェントとの対話の中で、 `Returns the input text as-is, Hello` や `myMCPServer(Hello)` と入力すると、自作のMCPサーバーが起動されると思います。
この例だと指定した文字列 `Hello` が返されると思います。 
（MCPサーバーのツールの許可を求められたらOKしてください）

うまく呼び出されない場合は `.rules` を設定してください👇

# .rules ファイル

`.rules` は、AIエージェントの回答に一定のルールを定めるためのファイルです。プロンプトのように記述することで、振る舞いを制御できます。

例えば次のように書いておきます。

```
`Returns the input text as-is, ${arg}` や `myMCPServer(${arg})` と聞かれたら、インストールされたMCPサーバーのmy-mcp-server-exampleのmyMCPServerを呼び出してください。

日本語で回答してください。
```

| AIエージェント | .rules ファイル |
| ---- | ---- |
| Copilot | [.github/copilot-customization.md](https://code.visualstudio.com/docs/copilot/copilot-customization) |
| Cline | .clinerules |
| Roo code | （Clineと同じファイル） |
| Cursor | .cursorrules |
| Claude Desktop | クロード設定 👉 クロードの応答において、どのような個人設定を〜 |


# さいごに

他にMCPサーバーは無いの？

https://github.com/modelcontextprotocol/servers/tree/main?tab=readme-ov-file

https://glama.ai/mcp/servers
