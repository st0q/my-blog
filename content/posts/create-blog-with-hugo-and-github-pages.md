---
title: "HugoとGitHub Pagesでブログを作る"
slug: create-blog-with-hugo-and-github-pages
date: 2021-12-20T23:10:15+09:00
with_date: true
tags: ["Hugo", "GitHub Pages", "GitHub Actions"]
draft: false
---


## はじめに

できるだけ手間を書けずに気軽にアウトプットできる場所を作ることを目的に、
本ブログサイトをHugoとGitHub Pagesを使用して作りました。<br>
作った時の手順を振り返りを兼ねてまとめました。

同様のブログを作りたいと思った方の参考になれば幸いです！

独自ドメインに変更する方法などについては今回は触れずに、最低限の構成で表示できるところまでをまとめています。<br>
カスタマイズした部分に関しては別途まとめる予定…です。

また、[Hugo](https://gohugo.io/)、[GitHub Pages](https://docs.github.com/ja/pages/getting-started-with-github-pages/about-github-pages)、[GitHub Actions](https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions)の詳細な説明は割愛します。


## プロジェクトの作成

まずは、公開するためのプロジェクトを作成します。<br>
Hugoのドキュメント[Quick Start](https://gohugo.io/getting-started/quick-start/)を参考に進めました。

### Hugoのinstall

Hugoのコマンドを使えるようにするため、Hugoをinstallします。<br>
自分はMacで作業していたので、Homebrew経由でinstallしました。

```sh
brew install hugo
```

### サイトの作成

hugoコマンドが使えることを確認しプロジェクトを作成します。<br>
適宜作業したいディレクトリに移動して以下のコマンドを実行します。

```sh
hugo new site quickstart
```

### Themeの追加

Hugoでは[theme](https://themes.gohugo.io/)というものが公開されており、themeを利用することで期待するデザインのサイトを作ることができます。

自分はシンプルなもので探して以下の3つが候補になりました。

  - [Etch \| Hugo Themes](https://themes.gohugo.io/themes/etch/)
  - [Noteworthy \| Hugo Themes](https://themes.gohugo.io/themes/hugo-theme-noteworthy/)
  - [mini \| Hugo Themes](https://themes.gohugo.io/themes/hugo-theme-cactus-plus/)

この中で「リポジトリの最後のcommitの時間、内容」および、「starの数」を考慮し[mini](https://themes.gohugo.io/themes/hugo-theme-cactus-plus/)にしました。

使用するthemeが決まったら、themeを追加します。

```sh
cd quickstart
git init
git submodule add https://github.com/nodejh/hugo-theme-mini.git themes/mini
```

追加が済んだらテーマのディレクトリをconfig.yamlに追加します。<br>
miniの場合は[sampleのconfig.yaml](https://github.com/nodejh/hugo-theme-mini/blob/master/exampleSite/config.yaml)が用意されているので、内容をコピーして必要な部分を適宜設定するようにするのが簡単で良いと思います。<br>
合わせて各項目の説明がドキュメントに書いてあるので、翻訳してそれぞれ確認しました。

以上で記事を書くための準備が完了です。

### 記事の作成

コマンドでファイルを追加します。<br>
ローカルでサーバーを起動して実際の表示を確認しながら記事を書くことができます。

```sh
// 新しい記事のファイルを作成
hugo new posts/my-first-post.md

// 実際に表示される内容を確認できるようにする
hugo server -D
```


## 記事の公開

ここからは記事を公開する手順について書いていきます。<br>
GitHubのドキュメント[Host on GitHub \| Hugo](https://gohugo.io/hosting-and-deployment/hosting-on-github/)を参考に進めました。


### リポジトリの作成

今回はProject PagesとしてGitHub Pagesを使用しました。<br>
リポジトリをブログのものとして分かりやすくしたかったのと、ドメインを変更する予定があったのでURLを気にする必要がなかったためです。

User(Organization) Pagesとした場合はURLが`https://<USERNAME|ORGANIZATION>.github.io/`のようになるのですが、Project Pagesの場合は`https://<USERNAME|ORGANIZATION>.github.io/<PROJECT>/`となり最後にプロジェクト名が入る形になります。

### GitHub Actionsのworkflowを追加

`.github/workflows/gh-pages.yml`に以下の内容のファイルを作成します。

```yml
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

### GitHub Pagesで公開するブランチの変更

作成したworkflowにより、`gh-pages`という名前のブランチにファイルが生成されます。<br>
そのため、GitHub Pagesで公開するブランチを`gh-pages`になるように変更します。<br>
「Settings > GitHub Pages」に設定画面があります。

![Settings > GitHub Pages](/images/create-blog-with-hugo-and-github-pages/setting-screen.png)

独自のドメインを使用する方法もあり、自分はそちらで対応しているのですが長くなるので別のタイミングでまとめようと思います。<br>
やり方は[GitHubのドキュメント](https://docs.github.com/ja/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)にまとまっています。

ここまでできると「Settings > GitHub Pages」の画面に表示されているURLにアクセスし、サイトが確認できるようになります。


## おわりに

基本的な構成であればドキュメントに書いてある内容に従うことで簡単にブログを公開することができました。<br>
独自ドメインの設定等カスタマイズした部分に関して、別途アウトプットしていこうと思います。

同様の手順でブログを作ろうと思った方の参考になれば幸いです。<br>
最後まで読んでいただきありがとうございました！










