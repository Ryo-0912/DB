## DBへJSONデータを入れる時に注意すること

=> **JSON形 {”key” : value} keyは必ず文字列(””)で囲む(’’で囲むとエラー)**

[参照URL](https://products.sint.co.jp/topsic/blog/json)

カンマで区切ると、キーと値の組み合わせを複数記述できる。

```
{“key1” : “value1”, “key2” : “value2”}
```
行数が多くなってくると読みにくくなってきます。その場合は改行とインデントを使う。

```
{
    “key1” : “value1”,
    “key2” : “value2”,
    “key3” : “value3”
}
```
