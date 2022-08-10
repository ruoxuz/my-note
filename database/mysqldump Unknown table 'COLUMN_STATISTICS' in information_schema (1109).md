# mysqldump Unknown table 'COLUMN_STATISTICS' in information_schema (1109)

## エラーメッセージ

    mysqldump: Couldn't execute 'SELECT COLUMN_NAME,                       JSON_EXTRACT(HISTOGRAM, '$."number-of-buckets-specified"')                FROM information_schema.COLUMN_STATISTICS                WHERE SCHEMA_NAME = 'jobpool' AND TABLE_NAME = 'BATCH_JOB_EXECUTION';': Unknown table 'COLUMN_STATISTICS' in information_schema (1109)

## 原因

MySQL 8以降に付属するmysqldumpでそれ以前のMySQL 5.7とかのサーバに対してダンプを実行したらこの問題が起こるらしい。

MySQL 8以降ではオプティマイザがヒストグラム統計というものを考慮するようになったため、mysqldumpもdumpをloadする際にヒストグラム統計をリセットさせる目的でANALYZE TABLE文を自動生成しようとする。そのときinformation_schema.COLUMN_STATISTICSを参照するが、そのテーブルがあるのはMySQL 8.0以降なのでそれ以前のバージョンのMySQLだと怒られが発生する、という具合のようだ。

## 対策

この問題はmysqldumpコマンドに --skip-column-statistics というオプションを設定して、ANALYZE TABLE文の自動生成をやめさせることで回避できる。

[mysqldumpコマンドで "Unknown table 'COLUMN_STATISTICS' in information_schema (1109)" と怒られる原因と対策](https://blog.pinkumohikan.com/entry/mysqldump-disable-column-statistics)