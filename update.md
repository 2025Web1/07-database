---
sort: 3
---
# UPDATE文

UPDATEのSQL文を定義するとき、更新する値の箇所に **「?」（プレースホルダという）** を使用します。
定義したSQL文をデータベース側に送信する前に`PDO`クラスの `prepare( )` （プリペアー）メソッドを実行し、`PDOStatement`オブジェクトを作成します。

作成した`PDOStatement`オブジェクトの `execute( )`（エクゼキュート）メソッドでデータベース側にSQL文を送ります。
そして、この `execute( )` メソッド実行時に、**「?」** の箇所に代入する値を指定しています。

**dbupdate.php**

<img src="./images/dbupdate_display.png" width="80%">

```php
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>dbupdate.php</title>
</head>

<body>
    <?php
    // データベースの接続アカウント情報
    $user = 'sampleuser';
    $password = 'samplepass';
    $host = 'db';
    $dbName = 'SAMPLE';
    $dsn = 'mysql:host=' . $host . ';dbname=' . $dbName . ';charset=utf8';

    try {
        // PDOを用いてデータベースに接続する
        $pdo = new PDO($dsn, $user, $password);
    } catch (PDOException $e) {
        // 接続できなかった場合のエラーメッセージ
        exit('データベースに接続できませんでした：' . $e->getMessage());
    }

    // 指定したuidのnameを変更するためのプレースホルダー入りのSQL文・・・①
    $sql = 'UPDATE person SET name = ? WHERE uid = ?';

    $stmt = $pdo->prepare($sql); // ・・・②

    // uid=5のnameを'野口五郎'に変更する・・・③
    $stmt->execute(['野口五郎', 5]);

    // SQLの定義: personテーブルから全てのレコードを取得する
    $sql = 'SELECT * FROM person';

    // $pdoを用いて$sqlを実行する
    $stmt = $pdo->query($sql);

    // 実行結果を $results に格納する
    $results = $stmt->fetchAll();

    // 配列をループして、各要素を出力する 'uid=UID, name=NAME'の形式で
    foreach ($results as $row) {
        echo 'uid=' . $row['uid'] . ', name=' . $row['name'] . '<br>';
    }
    ?>

</body>

</html>
```

**【解説】**

①: `$sql = 'UPDATE person SET name = ? WHERE uid = ?';`<br>
UPDATE文を定義するときに、値の部分を「?」（プレースホルダ）を用いて記述しています。
プレースホルダを用いた記述は、**SQLインジェクション対策**にもなります。

②: `$stmt = $pdo->prepare($sql);`<br>
定義したSQL文をデータベースに送信する前に`PDO`クラスの `prepare( )` メソッドを実行し、 `PDOStatement`クラスのオブジェクトを作成します。

③: `$stmt->execute(['野口五郎',  5]);`<br>
作成したPDOStatementオブジェクトの `execute( )` メソッドでデータベース側へSQL文を送信します。

このとき、「?」（プレースホルダ）の部分に値を代入するため、配列の形式でデータを引数として渡しますが、「?」（プレースホルダ）の順番と配列のデータの**順番を対応させる**ようにしてください。

サンプルコードの場合、`name = ?` の「?」に「野口五郎」を、`uid = ?` の「?」に「5」の値を代入しています。
つまり、『`uid=5`のデータの氏名を「糸居五郎」から「野口五郎」に変更する』というSQL文を実行しているのです。

なお、この`execute( )`メソッドの戻り値は、成功した場合に `TRUE` を、失敗した場合に `FALSE` を返します。
必要があれば戻り値（`TRUE`または`FALSE`）を受け取ればよいです。

③の行以降は、dbselect.phpと同じ処理内容で、テーブルpersonの全データを表示する処理を行っています。
