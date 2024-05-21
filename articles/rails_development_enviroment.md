---
title: "Rails 6.0.3の環境構築"
emoji: "🚂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Rails"]
published: true
---

# 概要
Ruby 2.6.6 / Rails 6.0.3で環境構築手順です。
いつものnokogiriでエラーが出たので、備忘録でメモします。
手順だけでなく、途中で調査したことやエラーなども記載してますので、ちょっと見づらくなってます…すみません。

# 環境
MacBook Pro / Apple M2 Pro / Sonoma 14.4.1
Ruby 2.6.6
rails 6.0.3.7
gem 3.0.3
rbenv 1.2.0
bundle 1.17.2

# 背景
パーフェクト Ruby on Rails(増強改訂版)をやろうとしたときに、冒頭の「1-1-2 gemコマンドでgemパッケージを扱う」でrailsのインストールをしたかった。
本書籍ではRuby 2.6.6, Rails 6.0.3で書かれているのでそのバージョンで環境構築する。

# 結論
`gem install`ではなく`bundle install`したら解決しました。

以下のGemfileを作成して、`bundle install`を実行すればOK。
```text:Gemfile
source 'https://rubygems.org'
gem 'rails', '6.0.3.7'
gem 'nokogiri', '1.13.10'
```

# やったこと
## ruby 2.6.6(rbenv)のインストール
まずはrbenvでruby 2.6.6をインストール。
早速エラー…。
```shell-session
$ rbenv install 2.6.6
ruby-build: using openssl@1.1 from homebrew
==> Downloading ruby-2.6.6.tar.bz2...
-> curl -q -fL -o ruby-2.6.6.tar.bz2 https://cache.ruby-lang.org/pub/ruby/2.6/ruby-2.6.6.tar.bz2
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 13.4M  100 13.4M    0     0  4757k      0  0:00:02  0:00:02 --:--:-- 4757k
==> Installing ruby-2.6.6...

WARNING: ruby-2.6.6 is past its end of life and is now unsupported.
It no longer receives bug fixes or critical security updates.

ruby-build: using readline from homebrew
ruby-build: using libyaml from homebrew
ruby-build: using gmp from homebrew
-> ./configure "--prefix=$HOME/.rbenv/versions/2.6.6" --with-openssl-dir=/opt/homebrew/opt/openssl@1.1 --enable-shared --with-readline-dir=/opt/homebrew/opt/readline --with-libyaml-dir=/opt/homebrew/opt/libyaml --with-gmp-dir=/opt/homebrew/opt/gmp --with-ext=openssl,psych,+
-> make -j 10

BUILD FAILED (macOS 14.4.1 on arm64 using ruby-build 20240416)

You can inspect the build directory at /var/folders/79/bvbghmmj1p1c460gz5zj3y4h0000gn/T/ruby-build.20240521134211.30305.K444U0
See the full build log at /var/folders/79/bvbghmmj1p1c460gz5zj3y4h0000gn/T/ruby-build.20240521134211.30305.log
```

上記ログファイルを見てみると、ffi-prep-closureでどうやらエラーとなっているみたい。
```shell-session
$ cat /var/folders/79/bvbghmmj1p1c460gz5zj3y4h0000gn/T/ruby-build.20240521134823.52767.log | grep error
(中略)
closure.c:264:14: error: call to undeclared function 'ffi_prep_closure'; ISO C99 and later do not support implicit function declarations [-Wimplicit-function-declaration]
6 warnings and 1 error generated.
```

[このIssue](https://github.com/ffi/ffi/issues/869)を参考に、RUBY_CFLAGを設定。
```shell-session
$ export RUBY_CFLAGS=-DUSE_FFI_CLOSURE_ALLOC
```

再度rubyのインストールを実行
```shell-session
$ rbenv install 2.6.6                                                                      
ruby-build: using openssl@1.1 from homebrew
==> Downloading ruby-2.6.6.tar.bz2...
-> curl -q -fL -o ruby-2.6.6.tar.bz2 https://cache.ruby-lang.org/pub/ruby/2.6/ruby-2.6.6.tar.bz2
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 13.4M  100 13.4M    0     0  11.1M      0  0:00:01  0:00:01 --:--:-- 11.1M
==> Installing ruby-2.6.6...

WARNING: ruby-2.6.6 is past its end of life and is now unsupported.
It no longer receives bug fixes or critical security updates.

ruby-build: using readline from homebrew
ruby-build: using libyaml from homebrew
ruby-build: using gmp from homebrew
-> ./configure "--prefix=$HOME/.rbenv/versions/2.6.6" --with-openssl-dir=/opt/homebrew/opt/openssl@1.1 --enable-shared --with-readline-dir=/opt/homebrew/opt/readline --with-libyaml-dir=/opt/homebrew/opt/libyaml --with-gmp-dir=/opt/homebrew/opt/gmp --with-ext=openssl,psych,+
-> make -j 10
-> make install
==> Installed ruby-2.6.6 to /Users/UserName/.rbenv/versions/2.6.6
```

::::details ChatGPTにRUBY_CFLAGを聞いてみた
`export RUBY_CFLAGS=-DUSE_FFI_CLOSURE_ALLOC` によってエラーが解決した理由について説明します。

### 要点

`export RUBY_CFLAGS=-DUSE_FFI_CLOSURE_ALLOC` は、Rubyのコンパイル時に特定の設定を有効にし、`ffi_prep_closure` 関数の問題を解決するために必要なFFI（Foreign Function Interface）のクロージャー処理を適切に行うためのマクロを定義しています。

### 詳細

エラーの原因は、`ffi_prep_closure` 関数が宣言されていないために発生します。これは、FFIライブラリの使用方法や構成が問題となっている可能性があります。`USE_FFI_CLOSURE_ALLOC` マクロを定義することで、この問題を回避します。

### 具体的な影響

1. **`ffi_prep_closure` の定義を明確にする**:
    - `ffi_prep_closure` 関数はFFIライブラリ内でクロージャー（関数オブジェクト）を準備するために使用されます。`USE_FFI_CLOSURE_ALLOC` マクロを設定することで、この関数が適切に定義され、使用されるようになります。
2. **ISO C99 の規則に従う**:
    - ISO C99規格では、関数が宣言されていない状態で呼び出されることを許可していません。このエラーは、関数が適切に宣言されていないために発生します。`USE_FFI_CLOSURE_ALLOC` を定義することで、FFIの実装がこの規則に従うようになります。
3. **メモリ管理の適用**:
    - `USE_FFI_CLOSURE_ALLOC` マクロは、FFIクロージャのメモリ管理方法を変更する可能性があります。これにより、クロージャの準備や呼び出しが適切に行われるようになり、エラーが回避されます。

### まとめ

`export RUBY_CFLAGS=-DUSE_FFI_CLOSURE_ALLOC` を実行することで、FFIライブラリ内の `ffi_prep_closure` 関数が正しく定義され、使用されるようになり、結果としてコンパイルエラーが解決します。この設定は、RubyのFFIの実装とCコンパイラの設定の間にある互換性の問題を解決するために必要なステップです。
::::

.ruby-versionファイルを作成し、rubyのバージョンを指定
```text:.ruby-version
2.6.6
```

rbenvにruby2.6.6がインストールされており、かつそれがアクティブになっていることを確認
```shell-session
$ rbenv versions  
  system
* 2.6.6 (set by /Users/UserName/workspace/perfect_ruby_on_rails/.ruby-version)
  2.6.10
  3.0.6
  3.1.2
$ ruby -v
ruby 2.6.6p146 (2020-03-31 revision 67876) [-darwin23]
```

## Railsのインストール

まずはとりあえず、書籍に書いてあるようにgem install railsをする。
nokogiriのエラーが発生。
```shell-session
% gem install rails -v 6.0.3.7 
(中略)
ERROR:  Error installing rails:
	The last version of nokogiri (>= 1.8.5) to support your Ruby & RubyGems was 1.13.10. Try installing it with `gem install nokogiri -v 1.13.10` and then running the current command again
	nokogiri requires Ruby version >= 3.0.0. The current ruby version is 2.6.6.146.
```

ざっくり意訳すると、
「最新のnokogiriはRuby 3.0.0以上が必要だけど、あなたが使っているのは2.6.6だよ」
「あなたが使っているRubyバージョンをサポートするnokogiriのバージョンは1.13.10だよ」
「`gem install nokogiri -v 1.13.10`を試してみてね」
とのこと。

rubygems.orgで確認すると、nokogiri 1.13.10はruby 2.6.0以上に対応していた。
https://rubygems.org/gems/nokogiri/versions/1.13.10

というわけで、nokogiri 1.13.10をインストール
```shell-session
$ gem install nokogiri -v 1.13.10
Fetching nokogiri-1.13.10.gem
Building native extensions. This could take a while...
Successfully installed nokogiri-1.13.10
Parsing documentation for nokogiri-1.13.10
Installing ri documentation for nokogiri-1.13.10
Done installing documentation for nokogiri after 7 seconds
1 gem installed
```

再度Railsのインストールを実行してみるが、またエラー。
```shell-session
$ gem install rails -v 6.0.3.7            
ERROR:  Error installing rails:
	There are no versions of nokogiri (~> 1.14) compatible with your Ruby & RubyGems. Maybe try installing an older version of the gem you're looking for?
	nokogiri requires Ruby version >= 3.0.0. The current ruby version is 2.6.6.146.
```

このあたりの記事を参考に調べてみた。
https://qiita.com/nanatsugasa/items/e43a6ba5e3faae83a0ee
https://qiita.com/murata0705/items/dfa2fed6c773f925a975

ざっくり要約すると、
- nokogiri 1.13.10をインストールした
- しかし、一部のgemパッケージ(rails-html-sanitizer)がnokogiri 1.14以上を要求している。
- rails-html-sanitizerはactionviewの依存関係になっている。

せっかくなのでrubygems.orgで確認してみる。

rails 6.0.3.7はactionview 6.0.3.7と依存関係にある。
https://rubygems.org/gems/rails/versions/6.0.3.7
![img.png](/images/rails_development_enviroment/rubygems_rails.png)

actionview 6.0.3.7はrails-html-sanitizeと依存関係にある。
https://rubygems.org/gems/actionview/versions/6.0.3.7
![img.png](/images/rails_development_enviroment/rugygems_actionview.png)

rails-html-sanitizer 1.6.0はnokogiri 1.14と依存関係にある。これがnokogiri 1.14以上を要求しておりエラーとなった。
https://rubygems.org/gems/rails-html-sanitizer
![img.png](/images/rails_development_enviroment/rubygems_railshtmlsanitizer.png)

rails-html-sanitizerは1.5.0であればnokogiriとの依存関係にない。
actionviewもrails-html-sanitizer 1.2.0以上を要求しているので、1.5.0でインストールすればよさそう。
![img.png](/images/rails_development_enviroment/rubygems_railshtmlsanitizer2.png)

手動でrails-html-sanitizer 1.5.0をインストールすればOKぽいのだが、試しにGemfileでrailとnokogiriのバージョンを指定してインストールしてみる。

```text:Gemfile
source 'https://rubygems.org'
gem 'rails', '6.0.3.7'
gem 'nokogiri', '1.13.10'
```

Gemfileからインストール
```shell-session
$ bundle install
```

インストールが完了したのでGemfile.lockを確認してみると、rails-html-sanitizer 1.5.0がインストールされていた。
```text:Gemfile.lock
    actionpack (6.0.3.7)
      actionview (= 6.0.3.7)
      activesupport (= 6.0.3.7)
      rack (~> 2.0, >= 2.0.8)
      rack-test (>= 0.6.3)
      rails-dom-testing (~> 2.0)
      rails-html-sanitizer (~> 1.0, >= 1.2.0)
    actionview (6.0.3.7)
      activesupport (= 6.0.3.7)
      builder (~> 3.1)
      erubi (~> 1.4)
      rails-dom-testing (~> 2.0)
      rails-html-sanitizer (~> 1.1, >= 1.2.0)
    rails-html-sanitizer (1.5.0)
      loofah (~> 2.19, >= 2.19.1)
```

gem installだとおそらく1.6.0をインストールしていたようなのに、bundle installだといい感じに1.5.0をインストールしてくれた。

最初から、bundle installすれば、今回のエラーや依存関係で悩むことなかったのでは…？

# 最後に
gem installで依存関係のエラーがでるようだったら、bundle installでやってみる。
