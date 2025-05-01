# データベースの利用(SELECT文)

データベースをプログラム上から利用するためには、SQL(Structured Query Language)という言語を使います。

SQLは、データベースに対して様々な操作を行うための言語であり、以下のような基本的な操作があります。

- SELECT文: テーブルからデータを抽出する
- INSERT文: テーブルにデータを挿入する
- UPDATE文: テーブルのデータ内容を更新する
- DELETE文: テーブルからデータを削除する

ここでは、まずSELECT文を使って、データベースからデータを抽出する方法について説明します。

## PDO（PHP　Data　Object）クラスの利用

PHPからデータベースへ接続するには `PDO`（PHP Data Objects）クラスを使用します。
この`PDO`クラスのオブジェクトは次の構文で作成します。

日本語の部分「接続するデータベースを利用するユーザー名」「そのパスワード」「接続するデータベース名」は、接続するデータベース環境によって異なります。

```php
// dsn(Data Source Name(データソース名))は、プログラム側が操作対象のデータベースを指定するための識別名
$user = '接続するデータベースを利用するユーザー名';
$password = 'そのパスワード';
$dsn = 'mysql:host=ホスト名;dbname=接続するデータベース名;charset=utf8'; 
$pdo = new PDO($dsn, $user, $password);
```

```tips
- `$dsn`に設定する値には、決してスペースを間に入れないように！エラーが発生する場合があります！
- `$dsn`に設定する値で、「：」（コロン）と「；」（セミコロン）を間違わないように！
```

また、以降のサンプルプログラムで出てくる`person`テーブルは、`db`ディレクトリ内のSQLによって、Dev Container起動時に既に構築済みです。
テーブル構造は、前章の[データベース利用](../db-create/README.md)で作成した`person`テーブルとほぼ同じものです。
いかに改めてテーブル構造を記載しておきます。

`person`テーブル構造

* `uid`: int型、主キーとして設定、 auto_increment付き
* `name`: varchar型、最大文字数20
* `company_id`: int型
* `age`: int型

以下が、初期値です。
なお`uid`は自動採番(auto_increment)されるため省略しています。

|  name |company_id|age|
|-------|---------:|--:|
|財津一郎|          1| 5|
|田宮二郎|          2|15|
|北島三郎|          3|25|
|伊東四朗|          3|35|
|糸井五郎|          1|45|
|鶴田六郎|          2|55|

## SELECT文

`dbselect.php`

![](./images/dbselect_display.png)

```php
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>dbselect.php</title>
</head>

<body>
    <?php
    // データベースの接続アカウント情報・・・①
    $user = 'sampleuser';
    $password = 'samplepass';
    $host = 'db';
    $dbName = 'SAMPLE';
    $dsn = 'mysql:host=' . $host . ';dbname=' . $dbName . ';charset=utf8';

    // 例外処理・・・②
    try {
        // PDOを用いてデータベースに接続する
        $pdo = new PDO($dsn, $user, $password);
    } catch (PDOException $e) {
        // 接続できなかった場合のエラーメッセージ
        exit('データベースに接続できませんでした：' . $e->getMessage());
    }

    // SQLの定義: personテーブルから全てのレコードを取得する
    $sql = 'SELECT * FROM person';

    // $pdoを用いて$sqlを実行する・・・③
    $stmt = $pdo->query($sql);
    // 実行結果を $results に格納する
    $results = $stmt->fetchAll();
    // 配列をループして、各要素を出力する 'uid=UID, name=NAME'の形式で
    foreach ($results as $row) {
        echo 'uid=' . $row['uid'] . ', name=' . $row['name'] . '<br>';
    }
    // データベースを切断する
    $pdo = null;
    ?>

</body>

</html>
```

①: データベース「SAMPLE」を利用するための設定値です。

以下の3つは、`env.txt`で設定した環境変数です。
`$user = 'sampleuser';`(ユーザー名)<br>
`$password = 'samplepass';`(パスワード)<br>
`$dbName = 'SAMPLE';`(データベース名)<br>

以下の`$host`はホスト名であり、`composer.yml` で設定されたデータベースコンテナのサービス名を使用します。
`$host = 'db';`<br>

②: PDOオブジェクトを作成するときは、データベースへの接続不良等のの例外が発生する可能性があるので、`try ～ catch` 構文を利用します。

③: `$stmt = $pdo->query($sql);`<br>
単純なSELECT文を実行する場合には、`PDO`クラスの `query( )` メソッドを使用します。<br>
戻り値は「`PDOStatement`（ピー・ディー・オー・ステートメント）オブジェクト」です。<br>
`->`（ハイフンと小なり記号）は「シングルアロー」と読み、Javaの「．」（ドット演算子）に相当します。
つまり、 `$pdo` が`PDO`クラスのオブジェクトであり、このオブジェクトが持つ `query` メソッドを呼び出しているのです。

④: `$results = $stmt->fetchAll( );`<br>
`PDOStatement`クラスの`fetchAll( )`メソッドで、SELECT文で取り出したデータを連想配列の形式で取り出します。

⑤: `$pdo = null;`<br>
処理が終了した後はデータベースへの接続を閉じておきます。
