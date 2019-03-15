# 演習用ノードの生成

> **難易度**: 基本

> **演習時間**: 約5分

この演習では、Puppet Boltで操作対象のノードを作成します。すでに対象ノードをお持ちの場合は、そのノードを利用することも可能です。
以下の3つのうち1つの方法でノードを準備してください。

- [既存ノード利用](#existing-nodes)
- [Vagrantでノード生成](#using-vagrant)
- [Dockerでノード生成](#using-docker)

# 手順
添付の構成ファイルを利用する場合、以下のいずれかを製品を事前にインストールする必要があります。 

- [Vagrant](https://www.vagrantup.com/) 
- [Docker for Mac](https://www.docker.com/docker-mac) 
- [Docker for Windows](https://www.docker.com/docker-windows) 

# 既存ノード利用

既存のノードを利用する場合は、Puppet BoltをインストールしたPCからSSHまたはWinWRmで接続できるように設定を行ってください。
* 詳細な変更手順がわからない場合は、VagrantまたはDockerを利用して演習ノードを生成して下さい

# Vagrantでノード生成
**注意事項** Vagrantに精通しており、適切な仮想環境の設定が終わっていることを前提に記載しています。

添付のVagrantfileは、CentOS 7 ノード3台と、Windows(nano Server)ノード1台を生成します。


1. `Vagrantfile`を下記の内容で作成するか[`Vagrantfile`](./Vagrantfile)からダウンロードしてください。もし、CentOSの台数を変更したい場合は、環境変数NODESを変更してください。


    ```ruby
    nodes_count = 3
    
    if ENV['NODES'].to_i > 0 && ENV['NODES']
      $nodes_count = ENV['NODES'].to_i
    end
    
    Vagrant.configure('2') do |config|
      config.vm.box = 'centos/7'
      config.ssh.forward_agent = true
      config.vm.network "private_network", type: "dhcp"
    
      (1..$nodes_count).each do |i|
        config.vm.define "node#{i}"
      end
    
      config.vm.define :windows do |windows|
        windows.vm.box = "mwrock/WindowsNano"
        windows.vm.guest = :windows
        windows.vm.communicator = "winrm"
      end
    end
    ```
2. コマンドラインのカレントディレクトリにVagrantfileがあることを確認して、 `vagrant up`.コマンドを入力します。

3. 次のコマンドで、Puppet Boltがノードに接続するための認証情報を表示します。

    ```
    vagrant ssh-config
    ```
    
    出力結果を保存することで、Boltを含むSSHクライアントから自動的に必要な認証情報を得ることができます。詳しい情報が必要な場合は、Linuxの[ssh_config](https://euske.github.io/openssh-jman/ssh_config.html)を確認してください。
    
    ```
    mkdir ~/.ssh
    vagrant ssh-config | sed /StrictHostKeyChecking/d | sed /UserKnownHostsFile/d >> ~/.ssh/config
    ```
    
    この設定を保存することで、ノードへのアクセスをIPアドレスではなくノード名で行うことができるようになります。Boltのコマンドから`--nodes node1,node2`と指定することで、ノードへアクセスすることができるようになります。

4. 全てのノードへsshで接続できることを確認してください。以前にVagrantを利用してSSH接続をしている場合 `~/.ssh/known_hosts`ファイルから古いSSH接続情報の削除を行ってください

    ```
    ssh node1
    ssh node2
    ssh node3
    ```


# Dockerでノード生成
Docker-composeを利用して複数のSSHサーバを生成することができます。 

1. `docker-compose.yml`を以下の内容で生成するか[docker-compose.yml](./docker-compose.yml)からダウンロードしてください。

    ```yaml
    version: '3'
    services:
      ssh:
        build: .
        ports:
          - 22
    ```
2. 同じディレクトリに`Dockerfile`を以下の内容で生成するか[Dockerfile](./Dockerfile)からダウンロードしてください。
    ```
    FROM rastasheep/ubuntu-sshd:16.04
    RUN ln -s /usr/bin/python3 /usr/bin/python
    ```

2. １台のSSHサーバを起動する場合 `docker-compose up -d`コマンドを実行します。複数のSSHサーバを起動する場合は `docker-compose up --scale ssh=3 -d`コマンドを入力します。 (ssh=3の数字は、起動するサーバの数です)

3. 起動しているサーバの数を確認するには`docker-compose ps`コマンドを入力します。
コマンド結果の例：
    ```
            Name                 Command        State           Ports
    -------------------------------------------------------------------------
    2acquiringnodes_ssh_1   /usr/sbin/sshd -D   Up      0.0.0.0:32768->22/tcp
    2acquiringnodes_ssh_2   /usr/sbin/sshd -D   Up      0.0.0.0:32769->22/tcp
    ```
    
    備考) Portsの列にコンテナへSSH接続するためのポートフォワーディングルールが表示されます。上記例のssh_1へPuppet Boltで接続する場合は、
    `127.0.0.1:32768`と指定します。
    
4. ローカルサーバにSSHクライアントがインストールされている場合は、SSHで接続できることを確認します。ユーザ名とパスワードは`root`です。接続TCPポート番号は`docker-compose ps`で確認したポート番号を指定してください。
    
    ```
    ssh root@127.0.0.1 -p 32768
    ```

5. 全てのノードへSSH接続ができることを確認してください。もし接続ができない場合は`~/.ssh/known_hosts`から重複エントリを削除してください。 

    Puppet Boletからノードに接続する場合は `--nodes 127.0.0.1:32768,127.0.0.1:32769`オプションを利用します。ポート番号は `docker-compose ps` コマンドで確認してください。

# 次の手順

テスト用のノードの設定が終わりましたら次のステップへ進んでください。

[コマンド実行](../03-running-commands)
