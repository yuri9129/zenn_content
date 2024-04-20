# Zenn CLI

* [📘 How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)

## 投稿方法
**新しい記事を作成**
```shell
npx zenn new:article
```

記事の設定
```
---
title: タイトル
emoji: 絵文字(1文字のみ)
       https://chat.openai.com/gpts/editor/g-22xxYFoaf
type: "tech" # tech: 技術記事 / idea: アイデアどちらか指定
topics: ["Ruby"]
published: true # false:非公開(下書き)
published_at: 2024-04-20 16:15 # 公開日時を指定(publishedがtrueの場合のみ)
---
```

記事の執筆
- [zennガイドライン](https://zenn.dev/guideline)
- 画像はimageフォルダ配下へ。mdファイルでは絶対パスで指定(`/images/xxx.jpg`)

プレビュー
```shell
npx zenn preview
```

投稿
```
mainブランチにpush
```