---
title: "Ruby Arrayチートシート"
emoji: "💎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [ "Ruby" ]
published: true
---

# 概要

RubyのArrayチートシート

# 配列の作成

| コード                   | 内容                | リンク                                                                  |
|-----------------------|-------------------|----------------------------------------------------------------------|
| `Array.new(n, v)`     | 要素数n 値vの配列を作成     | [LINK](https://docs.ruby-lang.org/ja/latest/method/Array/s/new.html) |
| `(n..m).to_a`         | nからmまでの整数配列を作成    |                                                                      |
| `(n..m).step(s).to_a` | nからmまでs刻みの整数配列を作成 |                                                                      |

:::details 配列の作成(例)
```ruby: Array.new(n, v)
Array.new(3, "A") # => ["A", "A", "A"]
# それぞれの要素を別オブジェクトにしたい場合は、ブロックを用いる
Array.new(3){Array.new(2, 0)} # => [[0, 0], [0, 0], [0, 0]]
```
```ruby: (n..m).to_a
(1..4).to_a # => [1,2,3,4]
(1...4).to_a # => [1,2,3]
(1..10).step(3).to_a # => [1,4,7,10]
```
:::

# 要素の取得
| コード                      | 内容                 | リンク                                                                        |
|--------------------------|--------------------|----------------------------------------------------------------------------|
| `arr[n]`                 | n番目の要素を取得          | [LINK](https://docs.ruby-lang.org/ja/latest/method/Array/i/=5b=5d.html)    |
| `arr[n,m]`               | n番目からm個分の要素を取得     | [LINK](https://docs.ruby-lang.org/ja/latest/method/Array/i/=5b=5d.html)    |
| `arr[n..m]`              | n番目からm番目までの要素を取得   | [LINK](https://docs.ruby-lang.org/ja/latest/method/Array/i/=5b=5d.html)    |
| `arr[n...m]`             | n番目からm-1番目までの要素を取得 | [LINK](https://docs.ruby-lang.org/ja/latest/method/Array/i/=5b=5d.html)    |
| `arr.values_at(n,m,...)` | n,m,...番目の要素を取得    | [LINK](https://docs.ruby-lang.org/ja/latest/method/Array/i/values_at.html) |
| `arr.last(n)`            | 最後からn個の要素を取得       |                                                                            |
| `arr.first(n)`           | 先頭からn個の要素を取得       |                                                                            |

:::details 要素の取得(例)
```ruby
arr = [1,2,3,4,5]

# 1番目の要素を取得
arr[1] # => 2

# 1番目から3つの要素を取得
arr[1,3] # => [2,3,4]

# 2~4番目の要素を取得
arr[2..4] # => [3,4,5]

# 複数要素を取得
arr.values_at(0,2,4) # => [1,3,5]
arr.values_at(*[0,2,4]) # 同じ

# 最後の要素をn個取得
arr.last(2) # => [4,5]

# 先頭の要素をn個取得
arr.first(2) # => [1,2]
```
:::

# 配列の結合

| コード                 | 内容          | リンク |
|---------------------|-------------|-----|
| `arr1 + arr2`       | 要素を結合       |     |
| `arr1.concat(arr2)` | 要素を結合(破壊的)  |     |
| `arr1 \| arr2`      | 重複なしで結合     |     |
| `arr1 - arr2`       | arr1から重複を削除 |     |
| `arr1 & arr2`       | 共通項を取得      |     |

:::details 配列の結合(例)
```ruby: arr1 + arr2
a = [1,2]
b = [3,4,5]
a + b # => [1,2,3,4,5]
```
```ruby: arr1.concat(arr2)
a = [1,2]
b = [3,4,5]
a.concat(b)
p a # => [1,2,3,4,5]
```
```ruby: arr1 |-& arr2
a = [1,2,3]
b = [3,4,5]

# 重複なしで結合
p a | b # => [1,2,3,4,5]

# 要素を除外
p a - b # => [1,2]

# 共通項を取得
p a & b # => [3]
```
:::


# 多重代入

| コード           | 内容             | リンク |
|---------------|----------------|-----|
| a,b,... = arr | 各要素をa,b,...に代入 |     |
| a,*b = arr    | 残りの要素をbに代入     |     |

:::details 多重代入(例)
```ruby
x = [1,2,3,4]
# 足りない要素は代入されない
a, b = x
p a # => 1
p b # => 2

# *で残りの要素を全てbに代入
a, *b = x
p a # => 1
p b # => [2,3,4]

# *は要素の途中でも使える
a, *b, c = x
p a # => 1
p b # => [2,3]
p c # => 4
```
:::



# splat演算子

| コード       | 内容           | リンク  |
|-----------|--------------|------|
| `f(*arr)` | arrを引数のように展開 | LINK |

:::details splat演算子(例)
```ruby
# どちらも同じ
arr.push(1, 2)
arr.push(*[1, 2])
```
```ruby

case x
  # どちらも同じ
when 'A', 'B'
when *['A', 'B']
```
:::

# 要素の追加・削除

| コード                      | 内容                | リンク |
|--------------------------|-------------------|-----|
| `arr.push(x,y,...)`      | 要素x,y,...を末尾に追加   |     |
| `arr << x << y <<...`    | 要素x,y,...を末尾に追加   |     |
| `arr.unshift(x,y,...)`   | 要素x,y,...を先頭に追加   |     |
| `arr.insert(i, v)`       | 要素vをi番目に追加        |     |
| `arr.delete(v)`          | 値がvの要素を削除         |     |
| `arr.delete_if{\|x\| x}` | ブロック評価がtrueの要素を削除 |     |

:::details 要素の追加・削除(例)
```ruby: push, <<
a = [1,2,3]
a.push(4) # [1,2,3,4]
a << 5 # [1,2,3,4,5]

# 複数要素を追加
a2 = [1,2,3]
a2.push(4,5) # [1,2,3,4,5]
# a2 << 4 << 5 と同じ
```
```ruby: unshift, insert
# 先頭に追加
b = [1,2,3]
b.unshift(0) # [0,1,2,3]

# 任意の位置に追加
c = [1,2,3]
c.insert(2, 99) # [1,2,99,3]
```
```ruby: delete
a = ['A', 'B', 'C']
a.delete('B')
p a # => ["A", "C"]
```
```ruby: delete_if
a = [1,2,3,4,5,6]
a.delete_if do |n|
  # 奇数のみ削除
  n.odd?
end
p a # => [2, 4, 6]
```
:::


# ループ

| コード                                    | 内容                        | リンク |
|----------------------------------------|---------------------------|-----|
| `arr.each{\|v\| ... }`                 | 各要素をブロックに渡す               |     |
| `arr.each_with_index{\|v, idx\| ... }` | インデックス付きでブロックに渡す          |
| `(n..m).each{\|n\| ... }`              | nからmまでの整数をブロックに渡す         |     |
| `(n..m).step(s).each{\|v\| ... }`      | nからmまでs刻みの整数をブロックに渡す      |     |
| `n.times{\|v\| ... }`                  | 0からn-1までの値をブロックに渡す(n回ループ) |     |
| `n.upto(m){\|v\| ... }`                | nからmまでの整数をブロックに渡す         |     |
| `n.downto(m){\|v\| ... }`              | nからmまでの整数をブロックに渡す         |     |

:::details ループ(例)
```ruby: each
arr = [1,2,3]
arr.each{|n| puts n} #=> 1 2 3
```
```ruby: each_with_index
arr = ["A", "B", "C"]
arr.each_with_index{|v,idx| puts "#{idx}:v"} # => "0:A" "1:B" "2:C"
```
```ruby: (n..m).each, step
(1..4).each{|n| puts n} # => 1 2 3 4
(1...4).each{|n| puts n} # => 1 2 3
(1..10).step(3){|n| puts n} # => 1 4 7 10
```
```ruby: times
3.times{print"Hey!"} # => Hey!Hey!Hey!
```
```ruby: upto, downto
5.upto(7){|n| puts n} # => 5 6 7
7.upto(5){|n| puts n} # => 7 6 5
5.step(10, 2){|n| puts n} # => 5 7 9
```
:::



# 配列の探索

| コード                                   | 内容                       | リンク |
|---------------------------------------|--------------------------|-----|
| `arr.map{\|v\| ... }`                 | 各要素を評価し、新しい配列を生成         |     |
| `arr.map.with_index{\|v, idx\| ... }` | 各要素評価し、新しい配列を生成(インデックス付) |     |
| `arr.select{\|v\| ... }`              | 条件を満たす要素を返却              |     |
| `arr.reject{\|v\| ... }`              | 条件を満たさない要素を返却            |     |
| `arr.find{\|v\| ... }`                | 条件を満たす最初の要素を返却           |     |
| `arr.sum{\|v\| ... }`                 | 各要素の合計を取得                |     |

:::details 配列の探索(例)
```ruby: map
# 各要素を評価し、新しい配列を返却
a = [1,2,3]
b = a.map{|n| n * 10}
p b # => [10, 20, 30]
```
```ruby: map.with_index
a = ["A", "B", "C"]
b = a.map.with_index{|x, i| "#{i}: #{x}"}
p b # => ["0: A", "1: B", "2: C"]
```
```ruby: select, reject
# 条件を満たす要素を返却
a = [1,2,3]
b = a.select {|n| n >= 2}
p b # => [2,3]

# 条件を満たさない要素を返却
a = [1,2,3]
b = a.reject {|n| n >= 2}
p b # => [1]
```
```ruby: find
# 条件を満たした最初の要素を返却
a = [1,2,3]
b = a.find{|n| n >= 2}
p b # => 2
```
```ruby: sum
# 各要素の合計を取得
a = [1,2,3]
b = a.sum
p b # => 6

# ブロックも指定可能
a = [1,2,3]
b = a.sum{|n| n**2}
p b # => 14
```
:::


# 文字列

| コード           | 内容          | リンク |
|---------------|-------------|-----|
| `arr.join(s)` | 各要素を文字列sで結合 |     |

:::details 文字列(例)
```ruby: join
# 各要素を結合
a = [1,2,3]
p a.join("+") # => "1+2+3"
```
:::

# 参考リンク
[Rubyリファレンス: class Array](https://docs.ruby-lang.org/ja/latest/class/Array.html)
[Rubyリファレンス: class Range](https://docs.ruby-lang.org/ja/latest/class/Range.html)