1 MicroSDにRaspberryPiOSを焼く(https://www.raspberrypi.com/software/) 2038年問題あるから64bitでカリッと焼こうね
2 HDDをとりあえずexFatでフォーマットしておく
3 TeraTarm等でラズパイにsshする (例:TeraTarm 接続先)

HDDをマウントする (https://beightlyouch.com/blog/programming/raspberry-pi-hdd/)

[OSのアップデート]
pi@foxcloud$
sudo apt update ←アプデ
sudo apt upgrade -y
(初回は時間がかかる)
[HDD接続確認]
sudo fdisk -l 
[HDDのフォーマット]
(買ったばかり等繋いですぐ使えるHDDの場合は)
Win10→ディスクの管理を開いて、ボリュームの削除をする
(参考 https://beightlyouch.com/blog/programming/raspberry-pi-hdd/)
pi@foxcloud$
sudo fdisk /dev/sda
n (new)
p (プライマリ)
Do you want to remove~~~ [Y]es/[N]o y
sudo mkfs.ext4 /dev/sda　←ぼくは！linuxの人！って教え込む
[HDDマウント]
pi@foxcloud$
sudo mkdir /var/snap/nextcloud/
→エラー
sudo e2fsck /dev/sda
→エラー
sudo mkfs -t ext4 /dev/sda
→成功
sudo mount /dev/sda /var/snap/nextcloud
[自動マウント]
pi@foxcloud$
sudo blkid /dev/sda
→UUIDコピー
sudo nano /etc/fstab
3行目に
UUID=91d554c5-d145-4b71-8b27-9f3b7e115e69  /var/snap/nextcloud ext4 defaults,nofail 0 0
(UUIDは適時変更)
sudo mount -a
→マウント
df
→認識されてるかどうか確認　(sdaが/var/snap/nextcloud にあるかどうか)
[nextcloudインストール](参考 https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-nextcloud-on-ubuntu-20-04-ja)
pi@foxcloud$
sudo apt install snapd -y ←snapをインストールする
sudo snap install nextcloud 
[セットアップ]
http://192.168.0.50
→接続が拒否される

sudo snap info nextcloud
→入ってるか確認
→入ってるけどなにかおかしい
[Nextcloudのアンインストール]
sudo snap remove nextcloud
→アンインストールさえできない
→ラズパイ再起動
→でもできない
 
 [ごたごた]
 cd /var/snap/nextcloud
 ls -la
 sudo apt remove snapd -y
 [マウントポイント変更]
sudo umount /dev/sda 
df
→マウントポイント削除確認
sudo rm -r /var/snap
→/var/snapの削除

sudo nano /etc/fstab
→マウント先変更
sudo mount -a
→マウント適用
df
→マウントされてるやつ一覧表示

sudo apt install snapd
→snap再インストール
sudo snap install nextcloud
→nextcloud再インストール
→エラー
sudo snap help refresh

cd /var
ls
cd /snap
→無いのにあるらしい
sudo snap remove nextcloud 
→nextcloud removed
sudo snap install nextcloud
→入れ直し
→nextcloud 24.0.3snap1 from Nextcloud? installed
→入った(っぽい)
→入ってないんかい
sudo nextcloud.manual-install admin foxcloudadmin
→ナンデヤネン入らんやん 
sudo snap run nextcloud.manual-install admin foxcloudadmin
→入った
[ドメインを信頼させる]
sudo snap run nextcloud.occ config:system:set trusted_domains 1 --value=192.168.0.*
sudo snap run nextcloud.occ config:system:set trusted_domains (使ってない番号) --value=(ドメイン)
[phpのRAM上限ぶっ飛ばす]
sudo snap set nextcloud php.memory-limit=-1M
→-1Mを変えればなんとかなる
}
<<NextCloud導入完了>>


おまけ
[Zsh導入]
sudo apt install git -y
sudo apt install zsh -y 
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"


