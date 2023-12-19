---
html: ledger-clio.html # Watch carefully for clashes w/ this filename
parent: clio-methods.html
blurb: レジャーのバージョン情報を取得します。
labels:
  - ブロックチェーン
---
# ledger
[[ソース]](https://github.com/XRPLF/clio/blob/master/src/rpc/handlers/Ledger.cpp "ソース")

`ledger`コマンドは公開されている[レジャー](ledgers.html)の情報を取得します。[新規: Clio v1.0.0](https://github.com/XRPLF/clio/releases/tag/1.0.0 "BADGE_BLUE")

Clioサーバはデフォルトで検証済みのレジャーデータを返すことに注意してください。

## リクエストのフォーマット
リクエストフォーマットの例:

<!-- MULTICODE_BLOCK_START -->

*WebSocket*

```json
{% include '_api-examples/ledger-clio/wsrpc-request.json' %}
```

*JSON-RPC*

```json
{% include '_api-examples/ledger-clio/jsonrpc-request.json' %}
```

<!-- MULTICODE_BLOCK_END -->

<!-- [Try it! >](websocket-api-tool.html#ledger) -->

リクエストには以下のパラメータを含めることができます。

| `Field`        | 型                    | 説明                    |
|:---------------|:----------------------|:-------------------------------|
| `ledger_hash`  | [ハッシュ][]           | _(省略可)_ 使用するレジャーバージョンの20バイトの16進文字列。([レジャーの指定][]ご覧ください。)  |
| `ledger_index` | [レジャーインデックス][] | _(省略可)_ 使用するレジャーの[レジャーインデックス][]、またはレジャーを自動的に選択するためのショートカット文字列。([レジャーの指定][]をご覧ください) |
| `transactions` | 真偽値                 | _(省略可)_ `true`の場合、指定されたレジャーバージョンのトランザクションに関する情報が返されます。デフォルトでは`false`です。レジャーバージョンを指定しない場合は無視されます。 |
| `expand`       | 真偽値                 | _(省略可)_ ハッシュのみではなく、トランザクション/アカウントの完全な情報がJSONフォーマットで提供されます。デフォルトでは`false`です。トランザクション、アカウント、またはその両方をリクエストしない場合は無視されます。 |
| `owner_funds`  | 真偽値                 | _(省略可)_ `true`の場合、応答のOfferCreateトランザクションのメタデータに`owner_funds`フィールドが含まれます。デフォルトでは`false`です。トランザクションが含まれておらず、`expand`がtrueではない場合には無視されます。 |
| `binary`       | 真偽値                 | _(省略可)_ `true`で、かつ`transactions`と`expand`が両方とも`true`の場合、JSONフォーマットではなくバイナリフォーマット（16進文字列）でトランザクション情報が返されます。 |
| `diff`         | 真偽値                 | _(省略可)_ `true`の場合、指定したレジャーのトランザクションの一部として追加、変更、または削除されたすべてのオブジェクトを返します。 |

`ledger`フィールドは廃止予定であり、今後予告なしに削除される可能性があります。

> **注記:** Clioの`ledger`コマンドは、[rippledのledgerコマンド](ledger.html)でサポートされている以下のフィールドをサポートしていません。
> 
> * `accounts`
> * `full`
> * `queue`
>
> 上記のフィールドのいずれかが`true`に設定されている場合、Clioは**常に**リクエストを`rippled`に転送します。

## レスポンスのフォーマット

処理が成功したレスポンスの例:

<!-- MULTICODE_BLOCK_START -->

*WebSocket*

```json
{% include '_api-examples/ledger-clio/wsrpc-response.json' %}
```

*JSON-RPC*

```json
200 OK

{% include '_api-examples/ledger-clio/jsonrpc-response.json' %}
```

<!-- MULTICODE_BLOCK_END -->

レスポンスは[標準フォーマット][]に従っており、正常に完了した場合は結果にレジャーに関する情報を表す次のフィールドが含まれています。

| `Field`                        | 型         | 説明                       |
|:-------------------------------|:-----------|:----------------------------------|
| `ledger`                       | オブジェクト | このレジャーの完全なヘッダーデータ。 |
| `ledger.account_hash`          | 文字列      | このレジャーのすべてのアカウントの状態情報のハッシュ値。 |
| `ledger.accountState`          | 配列        | (リクエストで指定がない場合省略) このレジャーのすべての[アカウント状態情報](ledger-data-formats.html)(16進数) |
| `ledger.close_flags`           | 数値        | このレジャーの[クローズに関するフラグ](ledger-header.html#close-flags)のビットマップ。 |
| `ledger.close_time`            | 数値        | レジャーが閉鎖された時刻（[Rippleエポック以降の経過秒数][]）。 |
| `ledger.close_time_human`      | 文字列      | 人間が読めるフォーマットでのこのレジャーが閉鎖された時刻。常にUTCタイムゾーンを使用します。 |
| `ledger.close_time_resolution` | 数値        | レジャー閉鎖時刻が丸められる秒数の範囲。 |
| `ledger.closed`                | 真偽値      | このレジャーが閉鎖されているかどうか。 |
| `ledger.ledger_hash`           | 文字列      | レジャー全体の一意の識別用ハッシュ。 |
| `ledger.ledger_index`          | 文字列      | このレジャーの[レジャーインデックス][]。整数を引用符で囲んだ形式で示されます。 |
| `ledger.parent_close_time`     | 数値        | 前のレジャーが閉鎖された時刻。 |
| `ledger.parent_hash`           | 文字列      | このレジャーの直前のレジャーの一意の識別用ハッシュ。 |
| `ledger.total_coins`           | 文字列      | ネットワークのXRPの合計（drop数）。整数を引用符で囲んだ形式で示されます。（トランザクションコストによりXRPが焼却されると、この値は減少します。） |
| `ledger.transaction_hash`      | 文字列      | このレジャーに記録されているトランザクション情報のハッシュ（16進数） |
| `ledger.transactions`          | 配列        | (リクエストで指定がない場合省略) このレジャーバージョンで適用されたトランザクション。デフォルトでは、メンバーはトランザクションの識別用[ハッシュ][]文字列です。リクエストで`expand`がtrueとして指定されている場合は、メンバーはJSONフォーマットまたはバイナリフォーマットでのトランザクションの完全な表現です。フォーマットは、リクエストで`binary`がtrueとして指定されていたかどうかに応じて決まります。 |
| `ledger_hash`                  | 文字列      | レジャー全体の一意の識別用ハッシュ。 |
| `ledger_index`                 | 数値        | このレジャーの[レジャーインデックス][]。 |
| `validated`                    | 真偽値      | _(省略される場合があります)_ `true`の場合、このレジャーは最終バージョンです。省略または `false` の場合、このレジャーのデータは最終版ではありません。 |
| `diff`                         | オブジェクト | _(リクエストで`diff`パラメータの指定がない場合省略)_ レジャーのトランザクションの一部として追加、変更、または削除されたハッシュの配列を含むオブジェクト。 |

リクエストに`"owner_funds": true`が指定されておりトランザクションが展開されている場合、応答には、各[OfferCreateトランザクション][]の`metaData`オブジェクトの`owner_funds`フィールドが含まれています。このフィールドの目的は、新しい検証済みレジャーごとに[オファーの資金化ステータス](offers.html#オファーのライフサイクル)を容易に追跡できるようにすることです。このフィールドの定義は、[オーダーブックサブスクリプションストリーム](subscribe.html#オーダーブックストリーム)でのこのフィールドのバージョンとはわずかに異なります。

| `Field`       | 値    | 説明                                         |
|:--------------|:------|:----------------------------------------------------|
| `owner_funds` | 文字列 | このレジャーのすべてのトランザクションの実行後に、このOfferCreateトランザクションを送信する`Account`が保有する`TakerGets`通貨の額。この通貨額が[凍結](freezes.html)されているかどうかはチェックされません。 |

リクエストで`”diff": true`を指定した場合、レスポンスにはオブジェクト`diff`が含まれます。このオブジェクトのフィールドは以下の通りです。

| `Field`       | 値                         | 説明                                         |
|:--------------|:---------------------------|:----------------------------------------------------|
| `object_id`   | 文字列                      | オブジェクトのID |
| `Hashes`      | オブジェクトまたは16進文字列 | リクエストが`binary`をtrueに設定したかfalseに設定したかに応じて、このフィールドは作成されたオブジェクトの内容、変更されたオブジェクトの新しい値、またはオブジェクトが削除された場合は空の文字列を返します。 |

### `diff`に`true`を設定した場合のレスポンス


````json
{% include '_api-examples/ledger-clio/jsonrpc-diff-response.json' %}
````

## 考えられるエラー

* [汎用エラータイプ][]のすべて。
* `invalidParams` - 1つ以上のフィールドの指定が正しくないか、1つ以上の必須フィールドが指定されていません。
* `lgrNotFound` - `ledger_hash`または`ledger_index`で指定したレジャーが存在しないか、存在してはいるもののサーバが保有していません。

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}