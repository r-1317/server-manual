# サーバー構築　手順書
## 環境
- PC: Gateway ME132-F14P
- OS: Ubuntu 22.04 

# 1. OSのインストール
## ダウンロード
- [Ubuntu_22.04_LTS](https://www.ubuntulinux.jp/download/ja-remix)をダウンロード
- ブータブルUSBを作成するため、[Rufus](https://rufus.ie/ja/)をダウンロード
### (任意)ハッシュ値の確認
以下のコマンドをPowerShellで実行
```ハッシュ値の確認
certutil -hashfile <isoファイルのパス> sha256
```
Ubuntuをダウンロードしたページにある`SHA256SUMS`の値と同じであることを確認
## ブータブルUSBの作成
画像1.1のようにrufusを実行する<br>
フォーマットオプションは初期状態で問題ない<br>
![](img/rufus.png)<br>
画像1.1　Rufusの設定
## インストール
### UEFIの設定
- 電源導入直後に(筆者の環境の場合)F2キーを押下
- `Boot`から`Boot priority order`を変更
- `USB HDD: Generic Flash Disk`を一番上に持ってくる<br>
  画像の状態でF6を押下すると1つ上に来る。
- `Exit`から`Exit Saving Changes`を選択し、変更を保存し終了

### Ubuntuのインストール
UEFIの設定を終えると、黒い画面が表示される<br>
一番上の`Try or Install Ubuntuを選択`
流れに沿ってインストールする
- `Ubuntuをインストール`を選択
- キーボードレイアウトを選択
- 選択したら`続ける`を選択
- Wi-Fiの設定をする。
- アップデートと他のソフトウェアの設定
  - デフォルトの設定で問題ない
- インストールの種類を選択
  - 今回はディスクを削除してのインストールを行うが、データが消えるため注意が必要である。
- タイムゾーンの選択
  - 日本在住であれば`Tokyo`を選択
- ユーザー名・パスワードなどを設定
  - コンピューターの名前は何でも良い
- 再起動する

しばらく待つ

- 再起動後、USBを外す
- 起動できないため、一度電源を切り、UEFIを起動する。
- UEFIの`Security-Set Supervisor password`を選択
  - 一時的なパスワードを入れる(何でも良い)
- `Select an UEFI file as tursted for executing`を選択
- `EMMC` → `EFI` → `ubuntu` → `shimx64.efi`の順に選択
  - ubuntu と名付ける
- パスワードを空に戻す
- Save and exitをする

Ubuntuが起動する。

<!--
以下を実行
sudo apt update
sudo apt upgrade
-->

# 2. Webサーバの構築
## Apache HTTP Serverのインストール
Apache HTTP Serverは、以下Apacheと呼称する。
以下のコマンドを実行
```
sudo apt update
sudo apt install apache2
```
### Apacheの起動
```
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```

### Apacheの動作を確認
- [http://localhost](http://localhost) にアクセス<br>
画像2.1のようなページが表示される。<br>
![Apache Default Page](default-page.png)<br>
画像2.1　Apache Default Page

## 仮想ホストの作成
### 設定ファイルの作成
以下のコマンドを実行<br>
`ファイル名`には任意のファイル名を代入
```
sudo touch /etc/apache2/sites-available/ファイル名.conf
```
### 設定ファイルの編集
設定ファイルに以下の内容を記述<br>
`ディレクトリパス`・`ファイル名`には任意のディレクトリパス・ファイル名を代入
```
<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot ディレクトリパス
	DirectoryIndex ファイル名
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### ディレクトリの作成
以下のコマンドを実行
```
sudo mkdir -p ディレクトリパス
```

### ファイルの作成
以下のコマンドを実行
```
sudo touch ディレクトリパス/ファイル名
```
作成したファイルにページの内容を記述する

### 仮想ホストの有効化
以下のコマンドを実行
```
sudo a2ensite <ファイル名>.conf
sudo systemctl reload apache2
```

### デフォルトの仮想ホストの無効化
以下のコマンドを実行
```
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

### ファイアウォールの有効化とポート開放
```
sudo ufw enable
sudo ufw allow ‘Apache Full’
```

## パスワード認証の設定
### apache2-utilsパッケージのインストール
以下のコマンドを実行
```
sudo apt install apache2-utils
```

### ユーザの登録
初回の場合、下のコマンドを実行
```
sudo htpasswd -c /etc/apache2/.htpasswd ユーザ名
```
2回目以降の場合、下のコマンドを実行
```
sudo htpasswd /etc/apache2/.htpasswd ユーザ名
```

### 設定ファイルの編集
仮想ホストの設定ファイルに認証のためのディレクティブを追加する<br>
以下の内容を追加
```
<Directory "認証を利用するディレクトリパス">
  AuthType Basic
  AuthName "Restricted Content"
  AuthUserFile /etc/apache2/.htpasswd
  Require valid-user
</Directory>
```

### Apacheの再起動
以下のコマンドを実行
```
sudo systemctl reload apache2
```



# 参考文献
[【Windows】SHA256などのファイルハッシュ値を確認する方法](https://qiita.com/setonao/items/4e9edd3e2064c56507e4)