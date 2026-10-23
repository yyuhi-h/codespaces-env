# codespaces-env

GitHub CodespacesでClaude Codeを使って開発するための環境（devcontainer設定とハンズオン資料）です。

## 必要なもの
- GitHubアカウント
  - Codespacesは個人アカウントに毎月の無料利用枠があり、超えた分は課金される。利用状況は[Billing](https://github.com/settings/billing)で確認できる
- Claudeの有料プラン（Pro / Maxなど）
  - Claude Codeの認証に使うトークン（`claude setup-token`）の発行に必要

## 使い始め方
このリポジトリを直接使うのではなく、テンプレートとして自分のアカウントにコピーしてから使います。

1. このページ右上の「Use this template」→「Create a new repository」
2. Ownerに自分のアカウントを選び、Repository nameは`codespaces-env`のままにする（ハンズオン内のパス`/workspaces/codespaces-env`と合わせるため。変えた場合はパスを読み替える）。Public / Privateはどちらでもよい
3. 作った自分のリポジトリで「Code」→「Codespaces」→「Create codespace on main」
4. 下の「Claude Codeの認証設定」の手順で、Claude Codeを使えるようにする

## Claude Codeの認証設定
Claude Codeのトークンを、GitHubのCodespaces secretsに`CLAUDE_CODE_OAUTH_TOKEN`という名前で登録します。登録したsecretは、Codespaceの中で環境変数として読み込まれます。

1. Codespaceのターミナルで`claude setup-token`を実行する。表示されたURLをブラウザで開いてClaudeにログインすると、`sk-ant-oat01-...`形式のトークンが表示される
2. [GitHubのCodespaces設定](https://github.com/settings/codespaces)を開き、「Codespaces secrets」の「New secret」で次のように登録する
   - Name：`CLAUDE_CODE_OAUTH_TOKEN`
   - Value：手順1のトークン
   - Repository access：自分の`codespaces-env`リポジトリ
3. Codespaceに戻り、secretの変更を知らせる通知が出たら再読み込みする。通知が出ないときは、Codespaceを一度停止して起動し直す
4. ターミナルで`claude`を実行し、ログインを求められずに起動すれば完了

> 手元のPCにClaude Codeが入っていれば、手順1はPCのターミナルで行ってもよいです。Codespaceを作る前にsecretを登録しておけば、手順3は不要です。

> トークンはパスワードと同じ扱いです。人に見せたり、ファイルに書いてコミットしたりしないでください。

## 環境の構成
Codespaceの中身は`.devcontainer/devcontainer.json`で決まっています。

```json
{
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu-24.04",
  "features": {
    "ghcr.io/anthropics/devcontainer-features/claude-code:1.0": {},
    "ghcr.io/devcontainers/features/node:1": {
      "version": "24",
      "pnpmVersion": "latest"
    },
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.14",
      "toolsToInstall": "uv"
    },
    "ghcr.io/devcontainers/features/github-cli:1": {}
  },
  "containerEnv": {
    "TZ": "Asia/Tokyo",
    "UV_LINK_MODE": "copy"
  }
}
```

- Ubuntu 24.04
- Claude Code
- Node.js 24 + pnpm
- Python 3.14 + uv（`UV_LINK_MODE=copy`はCodespacesでのハードリンク警告を回避するための設定）
- GitHub CLI（`gh`）：Codespacesでは認証済みの状態で使えるので、Claude Codeにプルリクエストの作成なども頼める
- タイムゾーンはAsia/Tokyo

### カスタマイズ
- Dockerは含まれていない。DB等をコンテナで動かす場合は`features`に`"ghcr.io/devcontainers/features/docker-in-docker:2": {}`を追加する
- `devcontainer.json`を変更したら、下の「リビルドのやり方」でCodespaceを作り直して反映する

### リビルドのやり方
1. コマンドパレットを開く
   - Mac：`Command + Shift + P`
   - Windows / Linux：`Ctrl + Shift + P`
   - ブラウザのショートカットと重なって開かないときは`F1`、または画面左下の「Codespaces」をクリック
2. `rebuild`と入力し、「Codespaces: Rebuild Container」を選ぶ
3. 確認が出たら「Rebuild」を押す。数分待つと、新しい設定でCodespaceが開き直る

> 作業中のファイルは`/workspaces/`の中にあれば消えません。ただし、`/workspaces/`の外にインストールしたものや置いたファイルは消えます。

## 環境変数の扱い
- 秘密情報（トークン・APIキー・DBパスワード等）は、Codespaces secrets（GitHubの設定画面）に保存する
- チームで共有してよい設定値（NODE_ENV、TZなど）は、`.devcontainer/devcontainer.json`の`containerEnv`に書く（反映にはRebuild Containerが必要）

## ハンズオン
- `docs/handson/`に、この環境で手を動かしながら学ぶためのメモを置いている。上から順に進める
  - [Git 超入門ハンズオン](docs/handson/git-handson.md)：このリポジトリを使って、branch / add / commit / push などGitの基本操作を覚える
  - [Claude Code 超入門ハンズオン](docs/handson/claude-code-handson.md)：Claude Codeに指示を出しながら、Next.js + MapLibre GL JSで地図に図形を描けるWeb-GISアプリを作る

## ライセンス
[CC0 1.0](LICENSE)（パブリックドメイン）。許可や著作権表示なしで、自由にコピー・改変・再配布できます。
