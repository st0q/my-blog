---
title: "GitHub Actionsのワークフローファイルをざっくり理解する"
slug: rough-understanding-github-actions
date: 2021-11-28T14:41:33+09:00
with_date: true
tags: ["GitHub Actions", "CI/CD"]
draft: false
---

## はじめに

GitHub Actionsを目にする機会が増え、興味があったのでtutorialをやってみました。<br>
そのときに学んだワークフローファイルについてざっくりとまとめました。

なんとなくわかった気になることを目的に書いたので、細かい部分に関しては言及していません。<br>
GitHub Actionsを触ったことがない方のドキュメントを読むきっかけになれば幸いです。


## ファイル内の構成

ワークフローファイルは以下の要素からできています。<br>
詳細を知りたい場合は[公式のドキュメント](https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions#workflows)で確認できます。

- ワークフロー<br>
ファイル内に定義されている内容のこと

- ランナー<br>
GitHub Actionsのワークフローからジョブを実行するアプリケーションのこと<br>
どの仮想環境で実行するかを指定することができる

- イベント<br>
ワークフローが実行されるタイミングのこと<br>
どのタイミングでワークフローを実行するかを指定することができる<br>
[指定できるタイミング一覧](https://docs.github.com/ja/actions/learn-github-actions/events-that-trigger-workflows)

- ジョブ<br>
同一のランナーで実行される一連のステップのこと<br>
複数のジョブは並列で実行される

- ステップ<br>
ジョブ内の個々のアクションまたはシェルコマンドのこと<br>
ステップには作成済みのアクションかシェルコマンドが指定される

- アクション<br>
何らかの処理が設定されているもの<br>
[公開されているアクション](https://github.com/marketplace?type=actions)を使用することで簡単にやりたいことが実現できる


## 実際のワークフローファイル

試しに公開されているアクションを使用して、リリース用のPRを作成するワークフローを作成してみました。<br>
今回のファイルではシェルコマンドを使う箇所がなく使用していないのですが、ステップでアクションではなくシェルコマンドを使うこともできます。

```yml
name: Create a release pull-request # ワークフローの名前を指定
on: workflow_dispatch # どのタイミングで実行されるか

jobs: # 以下にジョブを定義
  release_pull_request: # ジョブの名前を指定
    runs-on: ubuntu-latest # どのランナーで実行するか指定
    name: release_pull_request
    steps: # 以下にステップを定義
      - name: checkout # ステップの名前を指定
        uses: actions/checkout@v2 # 使用するアクションを指定
      - name: create-release-pr
        uses: grassedge/git-pr-release-action@v1.0
        with: # 以下でアクションに与えるパラメータを指定 (何が使えるかはアクションのドキュメントを確認)
          base: main
          head: develop
          token: ${{ secrets.GITHUB_TOKEN }}
          labels: release
          assign: true
```

合わせて公式のドキュメントにある[「ワークフローファイルの視覚化」](https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions#visualizing-the-workflow-file)を見ていただけると、イメージがつきやすいと思います。


## おわりに

複雑な記述を書かずに自動化できるのが良いと思いました。
まだ基本的なことしか試すことができていないので、他にも公開されているアクションを試してみようと思いました。

publicリポジトリであれば無料で試すことができるので、まだ試したことがなければぜひ試してみてください。
最後まで読んでいただきありがとうございました！


## 参考にしたサイト

- https://docs.github.com/ja/actions/learn-github-actions