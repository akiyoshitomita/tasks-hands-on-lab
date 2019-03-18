# コマンド実行

> **難易度**: 基本

> **所要時間**: 約5分

Puppet Boltはリモートホスト上で任意のコマンド実行を行うことができます。複雑なデプロイ方法を学ぶ前にこの動作を確認してください。テストノードのOS上で実行するコマンドを選びます。

- [Linux上でシェルコマンドを実行](#running-shell-commands-on-linux-nodes)
- [WindowsでPowershellコマンドを実行](#running-powershell-commands-on-windows-nodes)

# 前提条件
本章を学ぶ前に以下の作業を行ってください:

1. [Puppet Boltのインストール](../01-installing-bolt)
1. [テストノードの生成](../02-acquiring-nodes)

# Linux上でシェルコマンドを実行

Puppet BoltはデフォルトではSSHを利用して接続を行います。リモートホストへ接続できる場合、Boltを利用してシェルコマンドを実行することができます。`~/.ssh/config`に認証設定がしてあれば、設定内容に従って接続が行われます。

リモートノードでコマンドを実行する場合、以下のコマンド書式を利用します。
```
bolt command run <コマンド> --nodes <ホスト名又はIPアドレス>
```

SSH接続にユーザ名/パスワードを利用する場合は、以下のコマンド書式を利用します。
```
bolt command run <コマンド> --nodes <ホスト名又はIPアドレス> --user <ユーザ名> --password <パスワード>
```

1. システムの起動時間を表示する`uptime`コマンドを実行します。必要に応じて`node1`の部分は、接続先のIPアドレスやホスト名に修正してください。

    ```
    bolt command run uptime --nodes node1
    ```
    実行結果:
    ```
    Started on node1...
    Finished on node1:
      STDOUT:
         22:42:18 up 16 min,  0 users,  load average: 0.00, 0.01, 0.03
    Successful on 1 node: node1
    Ran on 1 node in 0.42 seconds

    ```

	**備考:** `Host key verification failed`と表示された場合は、`~/.ssh/known_hosts`ファイルを修正するか、boltコマンドに `--no-host-key-check`オプションを加えてください。

2. `uptime`コマンドをコンマ区切りの複数のノードに対して実行します。必要に応じて、`node1,node2,node3`の部分を接続のIPアドレスやホスト名に修正してください。`Host key verification failed`と表示された場合は、`--no-host-key-check`オプションを加えてください。

    ```
    bolt command run uptime --nodes node1,node2,node3
    ```
	実行結果:
    ```
    Started on node1...
    Started on node3...
    Started on node2...
    Finished on node2:
      STDOUT:
         21:03:37 up  2:06,  0 users,  load average: 0.00, 0.01, 0.03
    Finished on node3:
      STDOUT:
         21:03:37 up  2:05,  0 users,  load average: 0.08, 0.03, 0.05
    Finished on node1:
      STDOUT:
         21:03:37 up  2:07,  0 users,  load average: 0.00, 0.01, 0.05
    Successful on 3 nodes: node1,node2,node3
    Ran on 3 nodes in 0.52 seconds
    ```

3. 事前にノードをグループ化して一斉にコマンド実行を行うためのインベントリファイルを作成します。本手順では、インベントリファイル上のすべてのノードに対して実行する`all`グループを利用しますが、任意のグループ分けなどの方法はマニュアルをご参照願います。（英語）
    [Inventory File docs](https://puppet.com/docs/bolt/latest/inventory_file.html).

    Vaigrant環境での設定例を記します。 `~/.puppetlabs/bolt/inventory.yaml`ファイルに以下の内容を記して保存してください。

    ```yaml
    ---
    nodes: [node1, node2, node3]
    config:
      ssh:
        host-key-check: false
    ```

# WindowsでPowershellコマンドを実行

Boltは、WinRMを利用して、稼働中のWindowsノードに対してPowerShellコマンドを実行できます。リモートコマンド実行を行う場合、以下のコマンドを利用します。

```
bolt command run <command> --nodes winrm://<node> --user <user> --password <password>
```

補足 プレフィックス`winrm://`がノード名の前に付ける必要があります。また、認証用のユーザ名とパスワードを設定する必要があります。WinRMがSSL通信を許可しない場合は、`--no-ssl`オプションを付けてください。そうしないと、`unknown protocol`エラーが表示されます。

```
bolt command run <command> --no-ssl --nodes winrm://<node>,winrm://<node> --user <user> --password <password>
```

1. 環境変数にノードリストを設定を行います。本章より後の演習でも、このリストを利用するため中断した場合は環境変数を再設定してください。ユーザ名とパスワードはアドレスに書くことができます。Vagrantで作成した仮想マシンへ接続するときの例を下に記します。

    ```
    WINNODE=winrm://vagrant:vagrant@localhost:55985
    ```

    WindowsのPowerShell上で設定する場合は、以下のように入力してください。

    ```powershell
    $WINNODE="winrm://vagrant:vagrant@localhost:55985"
    ```

2.  リモートノード上でどう察しているプロセスリストを表示するコマンドを実行します。

    ```
    bolt command run "gps | select ProcessName" --nodes $WINNODE --no-ssl
    ```

    複数のノードに対して一斉にコマンド実行する場合、下のようにカンマ区切りでノードを設定してください。

    ```
    bolt command run <command> --nodes winrm://<node>,winrm://<node> --user <user> --password <password>
    ```


# 次の手順

リモートノードへのコマンド実行方法の学習が終わりましたら次のステップへ進んでください。

[スクリプト実行](../04-running-scripts)
