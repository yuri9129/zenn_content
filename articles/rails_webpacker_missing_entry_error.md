---
title: "[Rails6] Webpacker::Manifest::MissingEntryError"
emoji: "🚂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Rails"]
published: true
---

# 概要
Webpacker::Manifest::MissingEntryErrorの解決方法です。

# 環境
MacBook Pro / Apple M2 Pro / Sonoma 14.4.1
Ruby 2.6.6
rails 6.0.3.7
gem 3.0.3
rbenv 1.2.0
bundle 1.17.2
nvm 0.39.7
node v21.5.0

# エラー内容
Railsでscaffoldコマンドで簡単なページを作成、rails sでサーバーを立ち上げてページにアクセスしたところ「Webpacker::Manifest::MissingEntryError」というエラーが発生した。
![img.png](/images/rails_webpacker_missing_entry_error/img.png)

# 解決手順
GemのWebpakerのバージョンを5.4.4から5.4.3に変更し、再インストール

```text:Gemfile
gem 'webpacker', '5.4.3'
```

```shell-session:terminal
bundle install
```

bable.config.jsを修正
```javascript:babel.config.js
'@babel/plugin-proposal-private-methods',
'@babel/plugin-proposal-private-property-in-object',
↓
'@babel/plugin-transform-private-methods',
'@babel/plugin-transform-private-property-in-object',
```

nodeのバージョンを16に変更(v21だとだめだった)

nvmを入れている場合

```shell-session:terminal
$ nvm install 16
$ nvm use 16
$ nvm ls
->     v16.20.2
       v18.16.1
        v21.5.0
         system
$ node -v
v16.20.2
```

webpackをインストール＆コンパイル

```shell-session:terminal
rails webpacker:install
rails webpacker:compile
```

上記でもうまくいかない場合は、yarn.lockの削除、node_modulesフォルダの削除を行い、`yarn install`で再インストールしてから上記手順を再度行ってください。

# 参考

🔗 [Qiita:【Rails6】Webpacker::Manifest::MissingEntryErrorを解決する](https://qiita.com/ginger-yell/items/8584e9149496940ea144)

🔗 [Qiita: 【解決】Your Yarn packages are out of date! Please run `yarn install` to update.](https://qiita.com/___fff_/items/2df7fb111954fd0db4ed)

🔗 [Qiita: Webpacker::Manifest::MissingEntryErrorが発生](https://qiita.com/kndknd/items/18187f1c2199651d08d5)

🔗 [Qiita: node.jsのバージョンアップ、バージョン切り替え](https://qiita.com/strsk/items/925644e124efcc964625)

🔗 [Github Issues: Rails 6.1.x `rails new; rake assets:precompile` is broken](https://github.com/rails/rails/issues/48372)
