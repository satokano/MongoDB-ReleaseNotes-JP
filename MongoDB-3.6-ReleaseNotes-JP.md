<!-- Qiita https://qiita.com/kabao/items/ac14c779ea8ea110276f -->
<!-- 12/4時点で開発中だったが、12/5に正式版がでた。 -->
<!-- マニュアルっぽいので「ですます」で -->
<!-- 他のマニュアルを参照する場合、どちらかというと英語タイトルそのままで。文章の一部になっていて、訳さないと変な場合は日本語に訳す。 -->
<!-- NOTE: とかの囲みは、QiitaのMDで対応するものがなさそうなので、注意：brなどとしてごまかす？ ```text: かと思ったがそうすると中身のMD記法が解釈されない。divかと思ったがQiitaはstyle属性適用されない。divで囲んでタイトルはstrong、中身は自前でタグを書くのが見た目的には一番マシっぽい。 -->
<!-- SEE ALSO: は参照：、WARNING: は警告： -->
<!-- 英単語・数字の周りに空白をいれるか？ -->
<!-- restの`、``と、Qiita Markdownの対応は？ -->
[Release Notes for MongoDB 3.6](https://docs.mongodb.com/master/release-notes/3.6/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

脚注は、リリースノート以外の3.6のマニュアルなども参考にして今回付け加えたものです。

------------------------------

# MongoDB 3.6 リリースノート

## セキュリティ
### デフォルトでlocalhostにのみバインド
MongoDB 3.6から、MongoDBのバイナリ（[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)および[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)）は、デフォルトではlocalhostにのみバインドされるようになりました。以前はMongoDB 2.6以来、公式のMongoDB RPM（Red Hat, CentOS, Fedora Linux、およびその派生）、DEB（Debian, Ubuntu、およびその派生）のみがlocalhostにバインドするようになっていました。詳細は[Localhost Binding Compatibility Changes](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#bind-ip-compatibility)を参照してください。

### 認証の制限
データベースに対するユーザーの接続を、特定のIPアドレスからのみに制限するため、`authenticationRestrictions` パラメータが以下のコマンドやメソッドに対して追加されました。

| Commands | Methods |
|:-----------|:------------|
| [createUser](https://docs.mongodb.com/master/reference/command/createUser/#dbcmd.createUser) | [db.createUser()](https://docs.mongodb.com/master/reference/method/db.createUser/#db.createUser) |
| [updateUser](https://docs.mongodb.com/master/reference/command/updateUser/#dbcmd.updateUser) | [db.updateUser()](https://docs.mongodb.com/master/reference/method/db.updateUser/#db.updateUser) |
| [createRole](https://docs.mongodb.com/master/reference/command/createRole/#dbcmd.createRole) | [db.createRole()](https://docs.mongodb.com/master/reference/method/db.createRole/#db.createRole) |
| [updateRole](https://docs.mongodb.com/master/reference/command/updateRole/#dbcmd.updateRole) | [db.updateRole()](https://docs.mongodb.com/master/reference/method/db.updateRole/#db.updateRole) |

### 追加のセキュリティ改善
- TLS/SSL暗号化を使うときのOpenSSLの暗号アルゴリズムを制御するため、[opensslCipherConfig](https://docs.mongodb.com/master/reference/parameters/#param.opensslCipherConfig)パラメータが追加されました。
- 認証が有効な場合に限り、作成したカーソルに対し[getMore](https://docs.mongodb.com/master/reference/command/getMore/#dbcmd.getMore)を発行できます。
- [restore](https://docs.mongodb.com/master/reference/built-in-roles/#restore)ロールに対して[convertToCapped](https://docs.mongodb.com/master/reference/privilege-actions/#convertToCapped)アクションが追加されました。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/3.6-compatibility/#compatibility-enabled">後方非互換な機能</a></div>

## Aggregation
MongoDB 3.6からは以下の機能が利用可能です。

### より表現的な `$lookup`
[$lookup](https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#pipe._S_lookup) は複数の結合条件および非相関サブクエリが指定可能になりました。これは結合されたコレクションに対して変数の指定とパイプライン実行を許可することで可能になったものです。

[詳細は結合条件と非相関サブクエリ](https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#lookup-syntax-let-pipeline)の[$lookup](https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#pipe._S_lookup)シンタックスを参照してください。

### 新しいAggregationステージ
MongoDB 3.6では以下の新しいaggregationステージが追加されました。

| Operator | 説明 |
|:-----------|:------------|
| [$currentOp](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#pipe._S_currentOp) | [mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)インスタンスでアクティブまたは休止中の操作に関する情報を含むドキュメントのストリームを返します。新しいaggregationヘルパー [db.aggregate()](https://docs.mongodb.com/master/reference/method/db.aggregate/#db.aggregate) を使います。 |
| [$listSessions](https://docs.mongodb.com/master/reference/operator/aggregation/listSessions/#pipe._S_listSessions) | `config`データベースの`system.sessions`コレクションのサーバセッションを列挙します。新しいaggregationヘルパー[db.aggregate()](https://docs.mongodb.com/master/reference/method/db.aggregate/#db.aggregate)を使います。 |
| [$listLocalSessions](https://docs.mongodb.com/master/reference/operator/aggregation/listLocalSessions/#pipe._S_listLocalSessions) | サーバによりメモリ上にキャッシュされているサーバセッションを列挙します。 |

### 新しいAggregationオペレータ

| Operator | 説明 |
|:-----------|:------------|
| [$arrayToObject](https://docs.mongodb.com/master/reference/operator/aggregation/arrayToObject/#exp._S_arrayToObject) | キー・値のペアの配列をドキュメントに変換します。 |
| [$objectToArray](https://docs.mongodb.com/master/reference/operator/aggregation/objectToArray/#exp._S_objectToArray) | ドキュメントをキー・値のペアを表現するドキュメントの配列に変換します。 |
| [$mergeObjects](https://docs.mongodb.com/master/reference/operator/aggregation/mergeObjects/#exp._S_mergeObjects) | 複数のドキュメントを単一のドキュメントに結合します。 |
| [$dateFromString](https://docs.mongodb.com/master/reference/operator/aggregation/dateFromString/#exp._S_dateFromString) | 日付・時刻を表す文字列を日付オブジェクトに変換します。 |
| [$dateFromParts](https://docs.mongodb.com/master/reference/operator/aggregation/dateFromParts/#exp._S_dateFromParts) | 指定された日付の構成部品から、BSONの日付オブジェクトを構築します。 |
| [$dateToParts](https://docs.mongodb.com/master/reference/operator/aggregation/dateToParts/#exp._S_dateToParts) | 日付の構成部品を含むドキュメントを返します。 |

### 新しいAggregationヘルパー
MongoDB 3.6では、[$currentOp](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#pipe._S_currentOp)や[$listLocalSessions](https://docs.mongodb.com/master/reference/operator/aggregation/listLocalSessions/#pipe._S_listLocalSessions)で始まるものなど、もととなるコレクションを必要としないaggregationを処理するためのヘルパーとして [db.aggregate()](https://docs.mongodb.com/master/reference/method/db.aggregate/#db.aggregate) が追加されました。

### 新しいAggregation変数
[REMOVE](https://docs.mongodb.com/master/reference/aggregation-variables/#variable.REMOVE)にて、条件に応じたフィールドの除外が可能になりました。

### 新しいオプション
[aggregate](https://docs.mongodb.com/master/reference/command/aggregate/#dbcmd.aggregate)コマンドと[db.collection.aggregate()](https://docs.mongodb.com/master/reference/method/db.collection.aggregate/#db.collection.aggregate)メソッドは以下の新しいオプションをサポートします。

- どのインデックスを使うかを指定する`hint`オプション
<div><strong>注意：</strong><br />
<code>hint</code>は <a href="https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#pipe._S_lookup">$lookup</a>と<a href="https://docs.mongodb.com/master/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup">$graphLookup</a>ステージには適用されません。</div>

- データベースプロファイラ、currentOp、ログなどでオペレーションを追跡しやすくするための `comment`オプション

### タイムゾーンのサポート

MongoDB 3.6では[Aggregationの日付オペレーション](https://docs.mongodb.com/master/reference/operator/aggregation-date/)にて、タイムゾーンをサポートしました。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/3.6-compatibility/#aggregate-compatibility">Aggregation Compatibility Changes</a></div>

## 配列に対するupdateオペレータの改善
### arrayFilters
以下のコマンドとメソッドにおいて、配列においてどの要素を変更するのか指定するためのarrayFiltersパラメータが追加されました。

| Commands | Methods |
|:-----------|:------------|
| [findAndModify]() | [db.collection.findOneAndUpdate()]()<br />[db.collection.findAndModify()]() |
| [update]() | [db.collection.updateOne()]()<br />[db.collection.updateMany()]()<br />[db.collection.update()]() |
| | updateOneおよびupdateManyオペレーション向けの[db.collection.bulkWrite()]()<br />updateOne()とupdate()メソッドのBulk()オペレーション向けの[Bulk.find.arrayFilters()]() |

### 配列における複数要素のupdate
MongoDB3.6では、配列およびネストされた配列に対するupdateオペレーションにて、以下の位置指定オペレータが追加されました。

- [$[]](https://docs.mongodb.com/master/reference/operator/update/positional-all/#up._S_[])オペレータによる位置指定は、配列における全指定要素を更新します。
- [$[\<identifier\>]](https://docs.mongodb.com/master/reference/operator/update/positional-filtered/#up._S_[%3Cidentifier%3E])オペレータによる、フィルタ済みの位置指定は、[arrayFilters](https://docs.mongodb.com/master/release-notes/3.6/#arrayfilters)条件に合致する全指定要素を更新します。

### pushに対する負の配列インデックス
pushオペレータに対する[$position](https://docs.mongodb.com/master/reference/operator/update/position/#up._S_position)指定は、負の配列インデックスを指定可能で、配列の最終要素からの位置を示します。

## 3.6と互換性のあるドライバ
以下のドライバはMongoDB 3.6と互換性があります。

- Java 3.6
- Python 3.6
- C 1.9
- Node 3.0

### DNSにより構成されるシードリスト形式 mongodb+srv
標準的なコネクション形式に加えて、3.6のドライバはDNSにより構成されるシードリスト形式をサポートします。詳細は[DNS Seedlist Connection Format](https://docs.mongodb.com/master/reference/connection-string/#connections-dns-seedlist)を参照してください。

## Change Streams
MongoDB 3.6では、レプリカセット、および、レプリカセットと組み合わせたシャードクラスタに対して、change streamを作成することができるようになりました。

change streamにより、[oplog](https://docs.mongodb.com/master/reference/glossary/#term-oplog)を追跡する複雑性・リスクを避けながら、リアルタイムにデータ変更の通知を受けることができるようになりました。

3.6系ドライバであればいずれも、db.collection.watch()メソッドを使うことでchange streamを作成することができます。完全な手順や使い方は、それぞれのドライバのドキュメントを参照してください。

詳細は[Change Streams](https://docs.mongodb.com/master/changeStreams/#changestreams)を参照してください。

<div><strong>重要：</strong><br />
change streamを使うためには、<code>featureCompatibilityVersion</code>は"3.6"である必要があります。詳細は<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv">FeatureCompatibilityVersionを確認する方法</a>もしくは<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion">setFeatureCompatibilityVersion</a>を参照してください。</div>

## クライアントセッション
### 因果一貫性（Causal Consistency）
因果一貫性[^2]を提供するため、MongoDB 3.6ではクライアントセッションにおいて[causal consistency](https://docs.mongodb.com/master/core/read-isolation-consistency-recency/#causal-consistency)を有効にしています。因果一貫なクライアントセッションは、関連付けられた読み込みおよび *ack済みの* 書き込みの一連のオペレーションが因果関係を持つ、つまり順序通り反映されることを示します。クライアントアプリケーションは、一度に1つのスレッドだけがクライアントセッションでこれらの操作を実行するようにする必要があります。

アプリケーションはクライアントセッションを開始し、特定のセッションにオペレーションを関連付けることができます。アプリケーションは一度に1つのスレッドだけがクライアントセッションでこれらの操作を実行するようにする必要があります。[^3]

<div><strong>重要：</strong><br />
クライアントセッションを使うためには：<br />
<ul>
<li>クライアントには、3.6用に更新されたドライバが必要です。Java、C#、Python、Node、Cなど。</li>
<li>featureCompatibilityVersionは"3.6"である必要があります。詳細は<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv">FeatureCompatibilityVersionを確認する方法</a>もしくは<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion">setFeatureCompatibilityVersion</a>を参照してください。</li></ul></div>

### リトライ可能な書き込み

<div><strong>重要：</strong><br />
リトライ可能な書き込みを使うためには：<br />
<ul>
<li>クライアントには、3.6用に更新されたドライバが必要です。Java、C#、Python、Node、Cなど。</li>
<li>featureCompatibilityVersionは"3.6"である必要があります。詳細は<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv">FeatureCompatibilityVersionを確認する方法</a>もしくは<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion">setFeatureCompatibilityVersion</a>を参照してください。</li></ul></div>

MongoDB 3.6 から、レプリカセットとシャードクラスタに対する特定のack済みオペレーションは、一時的なネットワーク障害やレプリカセットのマスタ選出を適切に処理するため「リトライ可能」となりました。

リトライ可能な書き込み機能により、MongoDBのドライバは、ネットワーク障害やレプリカセットのファイルオーバー（その間レプリカセットにはプライマリが存在しなくなる）に遭遇した場合、自動的にこれらのオペレーションをリトライします。3.6ドライバでリトライ可能な書き込みを有効にするには、[retryWrites](https://docs.mongodb.com/master/reference/connection-string/#urioption.retryWrites)を参照してください。

リトライはただ1回だけ試みられるので、リトライ可能機能は一時的なネットワーク障害にのみ対応可能で、永続的なネットワーク障害には対応できません。

リトライ可能な書き込みについて詳細は、[Retryable Writes](https://docs.mongodb.com/master/core/distributed-queries/#retryable-writes)を参照してください。

### mongo シェルの変更
MongoDB 3.6では、[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルに対し[--retryWrites](https://docs.mongodb.com/master/reference/program/mongo/#cmdoption-retrywrites)コマンドラインオプションが追加されました。このオプションにより、[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルにおいて[リトライ可能な書き込み](https://docs.mongodb.com/master/release-notes/3.6/#retryable-writes)が有効になります。

[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルにおけるMongo()コネクションオブジェクトに、以下のメソッドが追加されました。

- [Mongo.isCausalConsistency()](https://docs.mongodb.com/master/reference/method/Mongo.isCausalConsistency/#Mongo.isCausalConsistency)
- [Mongo.startSession()](https://docs.mongodb.com/master/reference/method/Mongo.startSession/#Mongo.startSession)
- さまざまな [Session](https://docs.mongodb.com/master/reference/method/Session/#Session) メソッド
- さまざまな [SessionOptions](https://docs.mongodb.com/master/reference/method/SessionOptions/#SessionOptions) メソッド

## サーバセッション
MongoDBのサーバセッション（または論理セッションともいう）は、[クライアントセッション](https://docs.mongodb.com/master/release-notes/3.6/#client-sessions)において[因果一貫性（Causal Consistency）](https://docs.mongodb.com/master/core/read-isolation-consistency-recency/#causal-consistency)や[リトライ可能な書き込み](https://docs.mongodb.com/master/core/distributed-queries/#retryable-writes)をサポートするための、基盤となるフレームワークです。

<div><strong>重要：</strong><br />
アプリケーションは、クライアントセッションを使用してサーバセッションとのインターフェースを行います。</div>

サーバセッションはスタンドアロンmongod、レプリカセット、シャードクラスタにおいて利用可能です。

### サーバセッションのコマンド
3.6から、MongoDBのドライバは、ackされていない書き込みを除き、すべてのオペレーションをサーバセッションと関連付けるようになりました。環境構成が認証/認可を強制している場合は、サーバセッションは認証済みユーザと関連付けられます。

MongoDBクラスタのサーバセッションを、列挙、管理、killするために、以下のコマンドが利用可能です。

| Commands | 説明 |
|:-----------|:------------|
| [endSessions](https://docs.mongodb.com/master/reference/command/endSessions/#dbcmd.endSessions) | 指定のサーバセッションを終了します。 |
| [killAllSessions](https://docs.mongodb.com/master/reference/command/killAllSessions/#dbcmd.killAllSessions) | すべてのサーバセッションをkillします。 |
| [killAllSessionsByPattern](https://docs.mongodb.com/master/reference/command/killAllSessionsByPattern/#dbcmd.killAllSessionsByPattern) | 指定のパターンに一致するサーバセッションすべてをkillします。 |
| [killSessions](https://docs.mongodb.com/master/reference/command/killSessions/#dbcmd.killSessions) | 指定のサーバセッションをkillします。 |
| [refreshSessions](https://docs.mongodb.com/master/reference/command/refreshSessions/#dbcmd.refreshSessions) | アイドル状態のサーバセッションをリフレッシュします。 |
| [startSession](https://docs.mongodb.com/master/reference/command/startSession/#dbcmd.startSession) | 新しいサーバセッションを開始します。 |

### パラメータ
サーバセッションに対し以下の新しいパラメータが利用可能です。

- [logicalSessionRefreshMinutes](https://docs.mongodb.com/master/reference/parameters/#param.logicalSessionRefreshMinutes)
- [localLogicalSessionTimeoutMinutes](https://docs.mongodb.com/master/reference/parameters/#param.localLogicalSessionTimeoutMinutes)
- [maxAcceptableLogicalClockDriftSec](https://docs.mongodb.com/master/reference/parameters/#param.maxAcceptableLogicalClockDriftSecs)

### Aggregationステージ
サーバセッションをサポートするため、MongoDB 3.6では、以下のAggregationパイプラインステージが追加されました。

| Operator | 説明 |
|:-----------|:------------|
| [$listSessions](https://docs.mongodb.com/master/reference/operator/aggregation/listSessions/#pipe._S_listSessions) | `config`データベースの`system.sessions`コレクションの中のサーバセッションを列挙します。 |
| [$listLocalSessions]() | サーバによりメモリ中にキャッシュされているセッションを列挙します。[db.aggregate()](https://docs.mongodb.com/master/reference/method/db.aggregate/#db.aggregate)という新しいaggregationヘルパーを使用します。 |

### 一般的な事項
[serverStatus](https://docs.mongodb.com/master/reference/command/serverStatus/#dbcmd.serverStatus)は、[logicalSessionRecordCache](https://docs.mongodb.com/master/reference/command/serverStatus/#server-status-logicalsessions)の数に関する情報を返します。

### コマンドオプション
3.6から、MongoDBのドライバは、ackされていない書き込みを除き、すべてのオペレーションをサーバセッションと関連付けるようになりました。すべてのコマンドにおいてサーバセッションとの関連付けをサポートするために以下のオプションが利用可能です。
<div><strong>重要：</strong><br />mongoシェルとドライバはこれらのオプションをセッション中のコマンドに割り当てます。</div><br />

| Option | Type | 説明 |
|:-----------|:------------|:------------|
| `lsid` | Document | コマンドに関連付けられたセッションのユニークなidを指定するDocumentです。`txnNumber`が指定されている場合は、`lsid`も必須です。 |
| `txnNumber` | 64bitの整数 | コマンドのセッションにおいて、コマンドを一意に識別するための、狭義単調増加する非負の整数です。<br />このオプションが指定された場合は、`lsid`も必須です。 |

ステートメントの配列をとる[delete](https://docs.mongodb.com/master/reference/command/delete/#dbcmd.delete)、[insert](https://docs.mongodb.com/master/reference/command/insert/#dbcmd.insert)、[update](https://docs.mongodb.com/master/reference/command/update/#dbcmd.update)コマンドのために、以下のオプションも利用可能です。

<div><strong>重要：</strong><br />
<code>stmtIds</code>を手動で設定しないでください。MongoDBによって<code>stmtIds</code>は狭義単調増加となるように設定されます。</div>
<br />

| Option | Type | 説明 |
|:-----------|:------------|:------------|
| `stmtIds` | 32bit整数の配列 | 書き込みコマンドにおける各書き込みオペレーションをそれぞれ一意に識別する数値の配列です。 |

## JSON Schema
MongoDB 3.6は、JSON Schemaを使ったドキュメントバリデーションをサポートするため [$jsonSchema](https://docs.mongodb.com/master/reference/operator/query/jsonSchema/#op._S_jsonSchema) オペレータを追加しました。詳細は [$jsonSchema](https://docs.mongodb.com/master/reference/operator/query/jsonSchema/#op._S_jsonSchema) を参照してください。

$jsonSchemaを使うには、`featureCompatibilityVersion`は "3.6" にセットされている必要があります。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/3.6-compatibility/#compatibility-enabled">後方非互換な機能</a></div>

## レプリカセット
- レプリカセットのprotocol version 0（`pv0`）は非推奨となりました。レプリカセットのprotocol versionについて詳細は、[Replica Set Protocol Versions](https://docs.mongodb.com/master/reference/replica-set-protocol-versions/)を参照してください。
- [replSetResizeOplog](https://docs.mongodb.com/master/reference/command/replSetResizeOplog/#dbcmd.replSetResizeOplog)コマンドが追加され、レプリカセットのメンバーのoplogサイズを動的に変更できるようになりました。WiredTigerストレージエンジンを実行しているインスタンスにおいて利用可能です。
- [catchUpTakeoverDelayMillis](https://docs.mongodb.com/master/reference/replica-configuration/#repl-conf-catchup-takeover-delay) 設定オプションが追加され、あるノードが現在のプライマリよりも先にあることが決定されたあと、プライマリ選出のためにどのぐらいの時間待機するかを指定できるようになりました。
- protocol version 1（`pv1`）を使っているレプリカセットでは、アービタは、次の候補者以上の優先度をもつ正常なプライマリを検出している場合、NOを投票します。レプリカセットのprotocol versionについての詳細は[Replica Set Protocol Versions](https://docs.mongodb.com/master/reference/replica-set-protocol-versions/)を参照してください。
- レプリカセットのメンバーが、データ同期の実行中に[find](https://docs.mongodb.com/master/reference/command/find/#dbcmd.find)コマンドをどのくらいの時間待つべきかを調節するため、[oplogInitialFindMaxSeconds](https://docs.mongodb.com/master/reference/parameters/#param.oplogInitialFindMaxSeconds)パラメータが追加されました。
- `afterClusterTime`がoplogの最終適用時刻よりも大きい場合に、セカンダリがどのぐらいの時間待つべきかを指定するため、[waitForSecondaryBeforeNoopWriteMS](https://docs.mongodb.com/master/reference/parameters/#param.waitForSecondaryBeforeNoopWriteMS)パラメータが追加されました。

## シャードクラスタ
- [mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)が[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)に対してのコネクションを追加する速度を制御するため、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)に[ShardingTaskExecutorPoolMaxConnecting](https://docs.mongodb.com/master/reference/parameters/#param.ShardingTaskExecutorPoolMaxConnecting) パラメータが追加されました。
- マイグレーションされたチャンクが元のシャードから削除されるまでの最小の時間を決める [orphanCleanupDelaySecs](https://docs.mongodb.com/master/reference/parameters/#param.orphanCleanupDelaySecs) が追加されました。
- `config`データベースの中の`config.system.sessions` コレクションは、シャード化可能となりました。

## 一般的な改善
### MongoDB Compass のパッケージング
MongoDBサーバは、MongoDB Compass Community Editionの各プラットフォーム向けインストールスクリプトとともにパッケージ化されます。このスクリプトはMongoDBサーバのインストールプロセスの一環としてMongoDB Compassをインストールします。

### コレクション識別子
コレクションは、不変の一意な識別子を持ちます。`featureCompatibilityVersion` が "3.6" に設定されている必要があります。

<div><strong>参照：<br /></strong>
<a href="https://docs.mongodb.com/master/release-notes/3.6-compatibility/#compatibility-enabled">後方非互換な機能</a></div>

### 新しいクエリオペレータ
MongoDB 3.6では、以下の新たなクエリオペレータが追加されました。

- 新しい [$jsonSchema](https://docs.mongodb.com/master/reference/operator/query/jsonSchema/#op._S_jsonSchema) オペレータは、指定されたJSON Schemaで検証済みとなったドキュメントにマッチします。[$jsonSchema](https://docs.mongodb.com/master/reference/operator/query/jsonSchema/#op._S_jsonSchema)を使うには、`featureCompatibilityVersion` は "3.6" に設定されている必要があります。
- [$expr](https://docs.mongodb.com/master/reference/operator/query/expr/#op._S_expr)により、クエリ言語内で集計式が使えるようになりました。

### 削除されたオペレータ
MongoDB 3.6では、非推奨であった `$pushAll` オペレータが削除されました。詳細は[Remove $pushAll Update Operator](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#pushall-compatibility)を参照してください。

### インデックス

- インデックスは、入れ子になったドキュメント内のフィールドに対するクエリをカバー可能となりました。
- 複数キーインデックスは、どのフィールドが複数キーの原因となっているのかインデックスが追跡している場合には、配列以外のキーに対するクエリをカバー可能となりました。
- インデックスを作成するときは、インデックスの名前として `*` を指定することはできません。詳細は[Indexes Named `*`](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#index-asterisk)を参照してください。

### コマンド

- [listDatabases](https://docs.mongodb.com/master/reference/command/listDatabases/#dbcmd.listDatabases)コマンドに以下のオプションが追加されました。
  - `nameOnly`は、データベース名のみを返します。この場合はデータベースロックは不要です。（データベース名とサイズ情報の両方を返す場合は、データベースロックが必要となってしまいます。）
  - `filter`は指定条件に合致するデータベースのみを出力します。
- [validate](https://docs.mongodb.com/master/reference/command/validate/#dbcmd.validate)コマンドおよび[db.collection.validate()](https://docs.mongodb.com/master/reference/method/db.collection.validate/#db.collection.validate)メソッドの振る舞いが、変更されました。

### Wire Protocol と圧縮
- MongoDB 3.6では [OP_MSG](https://docs.mongodb.com/master/reference/mongodb-wire-protocol/#wire-op-msg) という新たな[Wire Protocol](https://docs.mongodb.com/master/reference/mongodb-wire-protocol/)のopcodeが導入されました。このopcodeのメッセージフォーマットは拡張可能であり、他のopcodeの機能を包含するように設計されています。
- `--networkMessageCompressors`オプション（または設定ファイル中の[net.compression.compressors](https://docs.mongodb.com/master/reference/configuration-options/#net.compression.compressors)）で利用するための`zlib`圧縮が追加されました。`--networkMessageCompressors`オプション（または[net.compression.compressors](https://docs.mongodb.com/master/reference/configuration-options/#net.compression.compressors)の設定）は[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)、[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェル、`OP_COMPRESSED`メッセージフォーマットをサポートするドライバの間でのネットワーク圧縮を有効化します。
- `snappy`ネットワーク圧縮が[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)、[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルの間でデフォルトで有効になりました。

### Read Concern
- 新たに ["available"](https://docs.mongodb.com/master/reference/read-concern/#readconcern."available")というRead Concernが導入されました。非シャード化コレクション（つまり、スタンドアロン環境か、レプリカセット環境）では、["local"](https://docs.mongodb.com/master/reference/read-concern/#readconcern."local")と["available"](https://docs.mongodb.com/master/reference/read-concern/#readconcern."available")のRead Concernは同じようにふるまいます。シャードクラスタでは、["available"](https://docs.mongodb.com/master/reference/read-concern/#readconcern."available")はクラスタパーティションに対してより強い耐性を持ちますが、チャンクマイグレーション中のシャードはorphan documentsを返す可能性があります。
<div><strong>参照：</strong><br /><a href="https://docs.mongodb.com/master/reference/parameters/#param.orphanCleanupDelaySecs">orphanCleanupDelaySecs</a></div><br />

- ["majority"](https://docs.mongodb.com/master/reference/read-concern/#readconcern."majority") read concernが常に有効化されました。これに伴い、[--enableMajorityReadConcern](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-enablemajorityreadconcern) と [replication.enableMajorityReadConcern](https://docs.mongodb.com/master/reference/configuration-options/#replication.enableMajorityReadConcern) は非推奨化されました。

### FTDC
MongoDB 3.6では、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)でDiagnostics Capture（FTDCともいわれる）の出力が追加されました。以前のバージョンでは、この機能は[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)のみで利用可能でした。[Diagnostic Parameters](https://docs.mongodb.com/master/reference/parameters/#param-ftdc)を参照してください。

<div><strong>注意：</strong><br />
FTDCはデフォルトで有効になっています。</div>

### 追加の改善点
MongoDB 3.6は以下の改善を含みます。

- [--bind_ip](https://docs.mongodb.com/master/reference/program/mongos/#cmdoption-bind-ip) オプションにおいて、Unixドメインソケットの完全なパスを指定できるようになりました。
- [mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod) は新たに [--timeZoneInfo](https://docs.mongodb.com/master/reference/program/mongos/#cmdoption-timezoneinfo) オプションを指定できるようになりました。タイムゾーンデータを指定するために使ってください。LinuxとmacOS向けのデフォルト設定では、``/usr/share/zoneinfo``にセットされています。
- 日付に関するオペレーションは、サポートされているすべてのオペレーティングシステムにおいて、日付範囲を一貫して受け入れられるようになりました。0年から9999年までの範囲の年を安全に扱うことができます。
- [mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)に対する[honorSystemUmask](https://docs.mongodb.com/master/reference/parameters/#param.honorSystemUmask)という新しい起動時オプションにより、MongoDBが新規作成するファイルは[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)プロセスを実行するユーザの[umask](https://en.wikipedia.org/wiki/Umask)で指定される読み込み/書き込み権限を持つようになります。LinuxおよびmacOSでのみ有効です。
- データベースに対する [maxWriteBatchSize](https://docs.mongodb.com/master/reference/limits/#Write-Command-Batch-Limit-Size) の制限が、1000 から 100000 に拡張されました。これはある1つのwrite batchの中で許可される書き込みオペレーションの最大数です。
- データベースコマンド [planCacheListPlans](https://docs.mongodb.com/master/reference/command/planCacheListPlans/#dbcmd.planCacheListPlans) はシェルでの[PlanCache.getPlansByQuery()](https://docs.mongodb.com/master/reference/method/PlanCache.getPlansByQuery/#PlanCache.getPlansByQuery) メソッドと同じものを出力します。両者からの出力は、プランが生成された時点のタイムスタンプを含むようになりました。
- [KeysRotationIntervalSec](https://docs.mongodb.com/master/reference/parameters/#param.KeysRotationIntervalSec)という新しいサーバパラメータにより[HMAC署名鍵](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code)がローテーションするまでの秒数を指定できるようになりました。

## 互換性への影響
いくつかの変更点は互換性に影響する可能性があり、ユーザーの対応が必要になるかもしれません。詳細な一覧は[MongoDB 3.6での互換性の変更点](https://docs.mongodb.com/master/release-notes/3.6-compatibility/)を参照してください。

## アップグレードの手順

<div><strong>注意：</strong><br />
3.4のインスタンスをアップグレードするためには、3.4のインスタンスは<code>featureCompatibilityVersion</code>が3.4に設定されている必要があります。詳細は<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv">FeatureCompatibilityVersionを確認する方法</a>もしくは<a href="https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion">setFeatureCompatibilityVersion</a>を参照してください。</div>

アップグレードの手順については、以下を参照してください。

- [スタンドアロン構成のサーバを3.6にアップグレードする](https://docs.mongodb.com/master/release-notes/3.6-upgrade-standalone/)
- [レプリカセットを3.6にアップグレードする](https://docs.mongodb.com/master/release-notes/3.6-upgrade-replica-set/)
- [シャードクラスタを3.6にアップグレードする](https://docs.mongodb.com/master/release-notes/3.6-upgrade-sharded-cluster/)

3.6へのアップグレードで支援が必要な場合は、[MongoDB社はメジャーバージョンアップグレードサービスを提供しています。](https://www.mongodb.com/products/consulting?jmp=docs)これは、あなたのアプリケーションを停止させることなく円滑な移行ができるように支援するものです。

## ダウンロード
MongoDB 3.6をダウンロードするには、[MongoDB Download Center](https://www.mongodb.com/download-center?jmp=docs#production)を参照してください。

## 3.6.0での既知の問題点

- [SEVER-31760](https://jira.mongodb.org/browse/SERVER-31760):<br />[$expr](https://docs.mongodb.com/master/reference/operator/query/expr/#op._S_expr)が、外部パイプラインで[$lookup](https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#pipe._S_lookup) aggregationステージの一部として使用される場合など、フィールドに対する等価一致でインデックスを使用しません。
- [TOOLS-1827](https://jira.mongodb.org/browse/TOOLS-1827):<br />SRVでURIを使用する場合、コマンドラインURIでクエリパラメータが指定されていない場合、フェッチされたTXTレコードは無視されます。これを回避するには、URIクエリ文字列で、データベースとの通信に使用するSSL設定（ssl=trueまたはssl=falseのいずれか）を明示的に指定します。
- [WT-3724](https://jira.mongodb.org/browse/WT-3724):<br />MongoDB 3.6は、macOS 10.13の新しいファイルシステムであるAPFS上ではテストされておらず、エラーが発生するかもしれません。

<div><strong>参照：</strong><br />
<a href="http://bit.ly/2jJFa85">3.6で解決された全てのJIRA issue</a></div>

## 問題を報告する
問題を報告するためには https://github.com/mongodb/mongo/wiki/Submit-Bug-Reports を参照してください。MongoDBサーバや、関連プロジェクトについて、JIRAチケットを登録する方法が書いてあります。

------------------------------

[^2]: Causal Consistency https://en.wikipedia.org/wiki/Causal_consistency http://www.cs.princeton.edu/~wlloyd/papers/causal-login13.pdf

[^3]: 原文でも同じことを書いている。
