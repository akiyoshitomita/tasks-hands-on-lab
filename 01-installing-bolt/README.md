# Puppet Boltのインストール

> **難しさ**: 基本

> **演習時間**: 約10分

Puppetタスクを実行できるようにPuppet Boltのインストールを行います。

# インストール方法

OSに合わせたパッケージをダウンろーして、インストーラーに従いインストールを行います。

* [Windowsインストーラ](https://downloads.puppet.com/windows/puppet6/puppet-bolt-x64-latest.msi) をダウンロードして、ダウンロードしたファイルを実行

* MAC OS インストーラ
  [10.11(EL Capitan)](https://downloads.puppet.com/mac/puppet6/10.11/x86_64/puppet-bolt-latest.dmg) 
  [10.11 (El Capitan)](https://downloads.puppet.com/mac/puppet6/10.11/x86_64/puppet-bolt-latest.dmg)
  [10.12 (Sierra)](https://downloads.puppet.com/mac/puppet6/10.12/x86_64/puppet-bolt-latest.dmg)
  [10.13 (High Sierra)](https://downloads.puppet.com/mac/puppet6/10.13/x86_64/puppet-bolt-latest.dmg)
  [10.14 (Mojave)](https://downloads.puppet.com/mac/puppet6/10.14/x86_64/puppet-bolt-latest.dmg)
  のをダウンロードして、ダウンロードしたファイルを実行
    
Linux版またはパッケージマネージャを利用してのインストール方法は[installation documentation](https://puppet.com/docs/bolt/latest/bolt_installing.html)を参照してください(英語) 

**注意事項** RubyのGemを利用してPuppet Boltするとサポートモジュールが含まれないため、お勧めいたしません。Gemからインストールした場合は、サポートモジュールを別途手動でインストールする必要があります。

# 次の手順

Puppet Boltのインストールが終わったら次へ進んでください。

[演習用ノードの生成](../02-acquiring-nodes)
