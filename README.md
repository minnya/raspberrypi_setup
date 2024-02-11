# RaspberryPi Setup Guide

## ssh設定
1. sshポート番号変更

    `/etc/ssh/sshd_config` ファイルを修正
    ```bash
    $ sudo nano /etc/ssh/sshd_config

    # The strategy used for options in the default sshd_config shipped with
    # OpenSSH is to specify options with their default value where
    # possible, but leave them commented. Uncommented options override the
    # default value.

    #Port 22
    Port 適当なポート番号
    #AddressFamily any
    ```

    参考サイト
    - https://gris-et-blanc.net/raspi/885/

2. 不正アクセス対策

    以下のコマンドでfail2banのインストール
    ```bash
    $ sudo apt-get install fail2ban
    ```
    設定ファイルをコピーして編集
    ```bash
    $ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
    $ sudo nano /etc/fail2ban/jail.local

    # 書き換える
    [default]
    # backend = auto
    backend = systemd
    
    # 以下を追記
    [ssh]
    enabled = true
    port = ssh
    filter = sshd
    logpath = /var/log/auth.log
    maxretry = 6
    ```
    サービスを起動
    ```bash
    $ sudo systemctl start fail2ban
    $ sudo systemctl status fail2ban
    ```

    参考サイト
    - https://gris-et-blanc.net/cloud/290/#fail2ban
    - https://memonotealpha.net/blog/archives/3190

## Dockerインストール
```bash
$ sudo apt update && sudo apt full-upgrade -y
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

Dockerをユーザー権限で実行できるようにグループに追加する。
```bash
$ sudo usermod -aG docker {ユーザー名}
```

バージョンの確認
```bash
$ docker -v
```

参考サイト
- https://raspida.com/rpi4-docker-install

## Swap領域の拡張
メモリが少ないモデルは、Swapを使用するとよい

`/etc/dphys-swapfile`を開く
```bash
$ sudo nano /etc/dphys-swapfile

# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
# CONF_SWAPSIZE=100
CONF_SWAPSIZE=2000 # 好きな容量を指定する (最大2000?)

# 設定を反映する
＄sudo /etc/init.d/dphys-swapfile restart

# 設定が反映されていることを確認する
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           7.6Gi       1.6Gi       4.2Gi       193Mi       2.1Gi       6.0Gi
Swap:           2.0Mi          0B        2.0Mi
```

参考サイト
- https://qiita.com/nyas/items/f4d0675061ee8cdcc3e7

