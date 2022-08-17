# aws rds timezone变更

AWSのRDS（mysql）のタイムゾーンがデフォルトだと、UTCだったので、Asia/Tokyoに変更する。

    mysql> select now();

    +---------------------+
    | now()               |
    +---------------------+
    | 2018-12-18 05:29:18 |
    +---------------------+

    mysql> show variables like '%time_zone';
    +------------------+-------+
    | Variable_name    | Value |
    +------------------+-------+
    | system_time_zone | UTC   |
    | time_zone        | UTC   |
    +------------------+-------+
    2 rows in set (0.00 sec)

## パラメータグループの作成

Amazon RDSの管理画面からパラメータグループを選択。
パラメータグループの作成を選ぶ。

![パラメータグループ](img/Screen%20Shot%202022-08-02%20at%2011.39.53.png)

パラメータグループの詳細で「グループ名」「説明」を記入して作成。
※今回はtimezone-asia-tokyoにします。

![パラメータグループの詳細](/img/Screen%20Shot%202022-08-02%20at%2011.45.22.png)

先程、作成したパラメータグループを選択して編集。
タイムゾーンを変更します。
検索窓に「time_zone」を入力。

![検索窓に「time_zone」を入力](/img/Screen%20Shot%202022-08-02%20at%2011.46.53.png)

time_zoneの項目を「Asia/Tokyo」に変更して保存します。

![「Asia/Tokyo」に変更](/img/Screen%20Shot%202022-08-02%20at%2011.47.01.png)

データベースのパラメータグループを変更
タイムゾーンを変更するデータベースを選択して編集。
データベースの設定の項目からDBのパラメータグループを先程作成したパラメータグループに変更して保存。

![DBのパラメータグループを先程作成したパラメータグループに変更](/img/Screen%20Shot%202022-08-02%20at%2011.47.11.png)

変更スケジュールをすぐに適用を選択して保存。

![変更スケジュールをすぐに適用を選択して保存](/img/Screen%20Shot%202022-08-02%20at%2011.47.18.png)

設定が終わったら、データベースを再起動します。

タイムゾーンが変更されたか確認
最後にタイムゾーンが変更されたか確認。

    mysql> show variables like '%time_zone';
    +------------------+------------+
    | Variable_name    | Value      |
    +------------------+------------+
    | system_time_zone | UTC        |
    | time_zone        | Asia/Tokyo |
    +------------------+------------+
    2 rows in set (0.00 sec)

これでOK！

[AWSのRDS（mysql）のタイムゾーンをAsia/Tokyoに変更する](https://qiita.com/chokosuki4400/items/8a37fb6e2c4f71c0bb42)
