---
sort: 5
---
# DELETE文

このDELETE文もUPDATE文、INSERT文同様に **「?」（プレースホルダ）** を利用してSQL文を定義します。

**dbdelete.php**

![](./images/dbdelete_display.png)

```php
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>dbdelete.php</title>
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

    // --- データの削除
    // プレースホルダーを用いてpersonテーブルから該当するnameを持つレコードを削除するSQLを準備・・・①
    $sql = 'DELETE FROM person WHERE name = ?';
    $stmt = $pdo->prepare($sql); // ②
    // 深沢七郎のレコードを削除・・・③
    $stmt->execute(['深沢七郎']);

    // --- 結果の取得
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
    // データベースを切断する
    $pdo = null;
    ?>

</body>

</html>
```

**【解説】**

①: `$sql = 'DELETE FROM person WHERE name = ?';`

DELETE文を定義するときも、値の部分を「?」（プレースホルダ）を用いて記述します。
このSQL文は、名前が一致するデータを削除するDELETE文を定義しています。

②: `$stmt = $pdo->prepare($sql);`

定義したSQL文をデータベースに送信する前に`PDO`クラスの `prepare( )` メソッドを実行し、 `PDOStatement`クラスのオブジェクトを作成します。

③: `$stmt->execute(['深沢七郎']);`

作成した`PDOStatement`クラスの `execute( )` メソッドで実際にデータベースへSQL文を送信する。
このとき、「?」（プレースホルダ）の部分に値を代入するため、配列の形式でデータを引数として渡します。
ここでは、名前が「深沢七郎」のデータを削除するDELETE文を実行します

③の行以降は、dbselect.phpと同じ処理内容で、テーブルpersonの全データを表示する処理を行っています。
