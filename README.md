# Flexget Configuration File

Konfigurasi flexget dengan _support file secrets_. Jelas bukan penggunaan yang sangat "menyeluruh", tapi cukup sesuai untuk penggunaan pribadi. Cukup menambahkan _folder series_ dan _movies_ kedalam library _[kodi](https://kodi.tv/)_, atau _[jellyfin](https://jellyfin.org/)_ dan semuanya berjalan dengan (hampir) otomatis.

Note: ini masih work in progress

## Dependencies:

* _[flexget](http://flexget.com/)_.
* _transmission_.
* _[kodi](https://kodi.tv/)_ atau _[jellyfin](https://jellyfin.org/)_ (optional).
* _telegram_ (optional, untuk notifikasi).

### Dependencies untuk _library flexget:_

* _transmissionrpc_ (untuk "komunikasi" antara _flexget_ dengan _transmission_). 
* _subliminal_ (untuk download subtitle).
* _python-telegram-bot_ (bot untuk sarana pengiriman notifikasi).

## Instalasi Flexget

Cara instalasi akan berbeda untuk setiap OS yang digunakan, dan khusus untuk linux/BSD ada beberapa metode instalasi, pilih yang sesuai. Lihat dokumentasi lengkap di web [mereka](https://flexget.com/Install).

### Linux

Yang perlu diperhatikan untuk kedua metode installasi _linux_ adalah untuk _scheduling_nya, apakah akan menggunakan _user_ tersendiri (baik untuk _flexget_ ataupun _transmission_), atau menggunakan _user_ yang sama dengan pengguna, atau menggunakan _user root_ (tidak disarankan).

#### Instalasi Metode Paling Mudah

Cukup ikuti metode [ini](https://flexget.com/InstallWizard/Linux),

#### Instalasi Metode Advanced

Ikuti petunjuk [ini](https://flexget.com/GitHubInstall).

## Konfigurasi

Untuk simpelnya, alur dari cara kerja _flexget_ adalah seperti ini:

* _flexget_ membutuhkan _transmission_ untuk melakukan _download_ (bisa juga menggunakan _deluge_ namun tentunya perlu perubahan pada _file_ konfigurasi ini). Disini tidak terlalu berpengaruh dengan _user_ apa _flexget_ berjalan, namun;
* _transmission_ membutuhkan hak akses terhadap _folder_ atau _directory_ yang dicantumkan didalam file _secrets.yml_.

Jadi penyesuaian yang perlu dilakukan adalah:

* Memastikan _user_ dari _process_ _flexget_, hal ini berdampak juga ke lokasi _directory_ konfigurasinya:
  * Jika _flexget_ ini akan dijalankan terpisah dari _user_ lain(misal dengan _user_ _flexget_, seandainya _flexget_ ingin tetap berjalan otomatis), metode ini berguna untuk _server_ misalnya. Maka perlu dibuat _user_ khusus. Untuk metode ini lokasi _directory_ konfigurasi perlu disesuaikan dengan _user_, _flexget_ hanya membutuhkan hak akses ke lokasi _file_ konfigurasi dan _log_(jika di_set_ berbeda dengan lokasi _directory_ konfigurasi).
  * Jika menggunakan _user_ yang sama dengan pengguna, tidak perlu banyak penyesuaian, lokasi _directory_ bisa di:
    * lokasi _path_ saat ini(seandainya tidak dijalankan dengan mode _daemon_),
    * lokasi _virtualenv_(seandainya instalasi menggunakan _virtualenv_),
    * _${HOME}/.flexget_ atau _${HOME}/.config/flexget_.
    Untuk jelasnya bisa lihat di[sini](https://flexget.com/Configuration).

### Konfigurasi untuk user terpisah

Buat satu _user_ khusus untuk menjalankan _flexget_, bisa juga menggunakan salah satu _user system_ yang ada, namun tidak disarankan karena biasanya semua _user system_ yang sudah ada memiliki tugas tersendiri.

``` shell
sudo useradd --system --shell /sbin/nologin --no-create-home --no-user-group -u 9000 flexget
```

_Clone_ _repository_ ini, ke sebuah _directory_ tersendiri(yang dapat diakses oleh _user_ yang baru saja dibuat). Untuk contoh disini dibuat lokasinya di _/usr/local/etc_.

``` shell
sudo su
mkdir -pv /usr/local/etc/flexget
cd /tmp
wget https://github.com/alexforsale/flexget/archive/master.zip
unzip master.zip
mv flexget-master/* /usr/local/etc/flexget
chown flexget:root /usr/local/etc/flexget -Rv
. bin/pip/activate
```

Jika menggunakan metode ini sebaiknya lokasi dari _file executable flexget_ sebaiknya jangan disimpan didalam _/home_. Misalkan dengan cara instalasi yang pertama:

``` shell
sudo mkdir -pv /usr/local/share && cd $_
sudo python3 -m venv flexget
cd flexget
sudo -H bin/pip install flexget
bin/pip install transmissionrpc subliminal python-telegram-bot
exit
```

#### Konfigurasi _transmission_

Biasanya disetiap _distro linux_ yang menyediakan _package_ _transmission_ dari _repository_ utama mereka, untuk _package transmission_ disediakan _user_ dan _group_ khusus dengan nama _transmission_. Jadi tidak perlu lagi dibuat _user_ tersendiri. Yang membedakan hanyalah _user_ yang memanggil _process transmission_ ini, misalkan dengan _user_ standar, biasanya _transmission_ akan mengikuti _id user_ standar tersebut. Jadi kita perlu memastikan _transmission_ dijalankan dengan _user_ _transmission_. Jadi perlu dipastikan _transmission_ memiliki akses untuk ke _directory_ download(contoh disini _/mnt/TOSHIBA_):

``` shell
# pastikan sebelumnya tidak ada process transmission yang berjalan
# check dengan perintah `pgrep -x transmission-da`
sudo chown -Rv :transmission /mnt/TOSHIBA/Videos
sudo chmod -Rv g+rws /mnt/TOSHIBA/Videos/
```

Jalankan _transmission-daemon_, biasanya jika untuk user tersendiri sudah disediakan _init service_-nya, baik untuk _systemd_ ataupun lainnya(seperti openRC).

``` shell
# sysv
sudo /etc/init.d/transmission-daemon start

# systemd
systemctl start transmission
```

Check log dengan perintah `sudo tail -f /var/log/transmission/transmission.log` (gunakan CTRL-c untuk _quit_).

#### Konfigurasi _flexget_

Jika ingin memakai konfigurasi yang sama dengan _repository_ ini, cukup lakukan modifikasi di _[file secrets.yml](#secrets.yml)_.

### Konfigurasi standar

Ini merupakan konfigurasi yang cukup mudah, tanpa memerlukan user khusus, semua _process_, baik _flexget_ ataupun _transmission_ menggunakan _user_ yang sama.

#### Konfigurasi _transmission_ (dan _flexget_)

Pastikan saja _transmission-daemon_ dipanggil oleh _user_ yang diinginkan, bisa melalui _systemd --user unit file_, melalui _desktop autostart_(biasanya di _~/.config/autostart_), _startup script X11_(_.xinitrc_, atau _.xprofile_) dan lainnya. _Autostart flexget_pun dapat dilakukan dengan cara yang sama.

## secrets.yml

Sebenarnya sudah cukup jelas penjelasan dalam _file_ _secret.yml.contoh_ yang tersedia didalam _repository_ ini, jadi ini hanya berupa tambahan - tambahan saja untuk beberapa poin yang memerlukan penjelasan yang lebih detail.

### thetvdb, pushbullet

Sepertinya untuk kedepannya saya hanya akan menggunakan _trakt_ saja untuk fitur _lookup_. Jadi kemungkinan besar _thetvdb_lookup_ akan dihapus dari _config.yml_. Sama halnya dengan _pushbullet_.

### telegram

Cukup rumit sebenarnya proses untuk menggunakan notifikasi _telegram_ ini,

* Pertama, buka link [ini](https://core.telegram.org/bots#6-botfather), dan jalankan semua instruksi yang tercantum disana.
  * `name` adalah nama yang nantinya akan terlihat didalam _telegram_.
  * `username` adalah nama bot-nya, dan memang harus diakhiri dengan `_bot`. Memiliki limitasi 32 karakter.
  * Untuk lainnya, gunakan perintah `/help` didalam _chat_ dengan _BotFather_.
* Masih didalam _telegram_, buka _chat_ baru dengan _bot_ tersebut, dan klik `START`.
* Didalam _file secrets.yml_, masukkan _token_ yang didapatkan dari _BotFather_, untuk _username_ didalam _secrets.yml_ adalah nama _user_ yang akan diberikan notifikasinya.

![alt text](img/Screenshot_2019-11-27-01-48-58-939_org.thunderdog.challegram.jpg "telegram bot")

## Konfigurasi Proxy

Untuk mudahnya, gunakan [Tor](https://www.torproject.org/) dan [Privoxy](http://www.privoxy.org/) (lihat _tutorial_ di[sini](https://wiki.archlinux.org/index.php/Privoxy), [sini](https://wiki.gentoo.org/wiki/Privoxy), atau [sini](https://help.ubuntu.com/community/Privoxy)), cek juga petunjuk untuk set _Forwarding through tor
_ didalam _wiki_ _archlinux_ atau _gentoo wiki_. Set proxy http ke 127.0.0.1:8118. Dan pastikan cek penggunaan proxy di commandline dengan perintah `http_proxy=$url-proxy curl $url-rss-feed` dengan $url-proxy adalah url proxy yang digunakan (misal seperti di contoh http://127.0.0.1:8118 atau http::/localhost:8118), dan url-rss-feed adalah url dari rss feed yang dipakai didalam file `config.yml`.

Masukkan url proxy di secrets.yml, dan uncomment baris ini (hapus tanda `#`):

```
#    proxy:
#      http: '{? proxy.http ?}'
```
