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
画像のようにrufusを実行する<br>
フォーマットオプションは初期状態で問題ない<br>
![](img/rufus.png)
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

### Ubuntuの設定



# 参考文献
[【Windows】SHA256などのファイルハッシュ値を確認する方法](https://qiita.com/setonao/items/4e9edd3e2064c56507e4)