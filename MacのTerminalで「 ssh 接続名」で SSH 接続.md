
# はじめに

　サーバーに接続する際、毎回下記のように接続するのは大変ですよね。

    $ssh ユーザー名@ホスト名 -i 秘密鍵ファイルのパス -p ポート番号
　なので、下記のように接続できるように設定します。

    $ssh 接続名

## 環境

macOS High Sierra 10.13.3

# 前提条件（重要）

接続情報（ホスト名、ユーザー名、ポート番号）が分かっている
SSH 秘密鍵取得済み（拡張子が ppk ならば OpenSSH 形式にする → 参考）
上記の情報で接続できる
接続するまで
１．~/.ssh ディレクトリの作成
２．秘密鍵ファイルを移動
３．~/.ssh/config ファイルを作成
４．サーバーに接続できるか確認

# １．~/.ssh ディレクトリの作成

    # 現在のユーザーで作成
    $ mkdir ~/.ssh
    
    # 所有者に読み・書き・実行権限を付与
    $ chmod 700 ~/.ssh

# ２．秘密鍵ファイルを移動

    # ~/.ssh ディレクトリへ移動
    $ mv 現在の秘密鍵ファイルのパス ~/.ssh/秘密鍵ファイル名
    
    # 所有者に読み込み権限を付与
    $ chmod 400 ~/.ssh/秘密鍵ファイル名

# ３．~/.ssh/config ファイルを作成

    # 現在のユーザーで作成・編集
    $ vi ~/.ssh/config
    
    ~/.ssh/config
    Host 接続名(自由)
        HostName ホスト名
        User ユーザー名
        IdentityFile ~/.ssh/秘密鍵のファイル名
        Port ポート番号
        TCPKeepAlive yes
        IdentitiesOnly yes
    　 vi で上書き保存してターミナルに戻る ← :wq
    
    # 所有者に読み・書き権限を付与
    $ chmod 600 ~/.ssh/config

# ４．サーバーに接続できるか確認

    $ssh 接続名

# 接続できない場合

　ファイルやディレクトリの所有者や権限が間違っていないか確認、~/.ssh/config の設定が間違っていないか確認する。それでも接続できない場合は教えてください(笑)

# おわりに

Windows では TeraTerm をインストールして使っていましたが、Mac は デフォルトで Terminal 使えるのほんと良いですね！ Mac 信者になりそうw （2018/10/6現在、Mac 信者になりましたw）

　もし記事でおかしい点・分かりにくい点があったり、もっと良い方法があったりしたら、どんどん突っ込んでもらえると助かります！！

# awsにssh接続が拒否された

    $ ssh -i aws-ssh-key.pem ec2-user@[IPaddress]
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    Permissions 0644 for 'aws-ssh-key.pem' are too open.
    It is required that your private key files are NOT accessible by others.
    This private key will be ignored.
    Load key "aws-ssh-key.pem": bad permissions
    ec2-user@[IPaddress]: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).

結論
    Permissions 0644 for 'aws-ssh-key.pem' are too open.という文字通りアクセス権限が問題。
    なので、$ chmod 700 aws-ssh-key.pemとすれば、問題解決。

control+d退出

[MacのTerminalで「 ssh 接続名」で SSH 接続](https://qiita.com/blendthink/items/d31e5c19751644129efc)

[awsにssh接続が拒否された。](https://qiita.com/hanlio/items/c8a89244be67b1ec5b63)
