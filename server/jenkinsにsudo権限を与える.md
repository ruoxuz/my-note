# jenkinsにsudo権限を与える

jenkinsのビルド実行ユーザーは
「jenkins」というユーザーです。
なので、このユーザーにsudo権限を与えてあげます。

コマンド「visudo」で編集可能なファイル「/usr/sbin/visudo」の
中にはこんな記述があります。

    /#includedir /etc/sudoers.d

ですので、このディレクトリ配下にファイルを配置すれば、
自動的に読み込んでくれることが分かります。

    $ cd /etc/sudoers.d

ディレクトリに移動して、

    $ touch jenkins

「jenkins」ファイルを作成します。

    $ vi jenkins

「jenkins」ファイルの中身を編集します。

    jenkins ALL=(ALL) NOPASSWD:ALL

これで、jenkinsユーザーにsudo 権限を与えることができました。