---
sort: 7
---

# 課題の解答

以下は、課題の解答例です。
課題に合格されなかった方は、こちらのコードを参考にしてください。

**dbselect1.php**

```php
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>dbselect1.php</title>
</head>

<body>
    <h1>条件付きSELECTの例</h1>
    <?php
    if (empty($_POST['uid'])) {
    ?>
        <form action="dbselect1.php" method="POST">
            <input type="text" name="uid">
            <input type="submit" value="検索">
        </form>
    <?php
    } else {
        $user = 'sampleuser';
        $password = 'samplepass';
        $host = 'db';
        $dbName = 'SAMPLE';
        $dsn = 'mysql:host=' . $host . ';dbname=' . $dbName . ';charset=utf8';

        try {
            $pdo = new PDO($dsn, $user, $password);
        } catch (PDOException $e) {
            exit('データベースに接続できませんでした：' . $e->getMessage());
        }
        $uid = $_POST['uid'];
        $sql = 'SELECT * FROM person WHERE uid = ?';
        $stmt = $pdo->prepare($sql);
        $stmt->execute([$uid]);
        $row = $stmt->fetch();
        if (empty($row)) {
            echo '該当するユーザがいませんでした(uid=' . $uid . ')';
        } else {
            echo 'uid=' . $row['uid'] . ', name=' . $row['name'];
        }
        $pdo = null;
    }
    ?>
</body>

</html>
```
