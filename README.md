Flexget Configuration File
========

Konfigurasi flexget dengan support file secrets. Jelas bukan penggunaan yang sangat "menyeluruh",
tapi cukup sesuai untuk penggunaan pribadi. Cukup menambahkan folder series dan movies kedalam library [kodi](https://kodi.tv/)
dan semuanya berjalan dengan (hampir) otomatis.

Note: ini masih work in progress

Saat ini fitur yang berjalan:
* ~~mengimpor list database tv series dari thetvdb.com~~
* mengimpor list database movies dari trakt.tv
* mencari torrent dari rss secara berkala
* support download manual, cukup taruh file .torrent difolder `'{? staging.series ?}'`atau `'{? staging.movies ?}'` akan otomatis diproses oleh flexget
* download 2 film random per hari dengan rule: imdb rating >7, vote > 800, rilis >2005
* memindahkan torrent yang selesai didownload ke folder target
* menghapus list torrent yang selesai didownload dari transmission
* download subtitle secara otomatis untuk file yang didownload
* notifikasi melalui ~~pushbullet~~ dan telegram
* Download Series Premiere untuk TV Series baru

Dependencies:
--------

* [flexget](http://flexget.com/), untuk archlinux bisa gunakan package `flexget-git` dari [AUR](https://aur.archlinux.org/packages/flexget-git)
* transmission-cli
* transmissionrpc (pip install Transmissionrpc), atau untuk archlinux, dengan package AUR [python-transmissionrpc](https://aur.archlinux.org/packages/python-transmissionrpc)
* Linux (jelas)
* [kodi](https://kodi.tv/)
* subliminal untuk download subtitle (pip install subliminal), untuk archlinux menggunakan package AUR [subliminal](https://aur.archlinux.org/packages/subliminal)
* telegram bot (pip install python-telegram-bot), atau untuk archlinux: [python-telegram-bot](https://www.archlinux.org/packages/community/any/python-telegram-bot/)

Instalasi Flexget
-------

Cara instalasi akan berbeda untuk setiap OS yang digunakan, dan khusus untuk linux/BSD ada beberapa metode instalasi, pilih yang sesuai. Lihat dokumentasi lengkap di web [mereka](https://flexget.com/Install).

Cara penggunaan
-----

Clone repository ini ke `~/.flexget`, atau `~/.config/flexget` jika dilinux/BSD. Sebenarnya bisa dimana saja, tapi jika diluar kedua path itu perlu dicantumkan ketika eksekusi flexget dengan perintah `flexget -c /path/ke/config.yml`.

Note: jika menggunakan virtualenv untuk metode instalasi flexget-nya, perlu diingat instalasi dependencies harus dilakukan didalam virtualenv (`source /path/ke/flexget/bin/activate` ini hanya berlaku untuk terminal yang digunakan saat itu). Install semua yang dibutuhkan seperti kodi (bukan mandatory, tetapi cukup bagus.), flexget, transmissionrpc dan lainnya, informasi instalasi di [flexget.com](http://flexget.com/Install) sepertinya sudah mengcover semuanya. Set folder library di kodi sesuai dengan yang dicantumkan di secrets.yml. File secrets.yml ini adalah ekstensi dari file konfigurasi yang ditentukan di baris awal config.yml, memungkinkan untuk digunakan oleh banyak user tanpa harus melakukan perubahan langsung di config.yml.

Pertama copy file secrets.yml.contoh ke file secrets.yml, lalu ubah isinya:

Ada dua jenis folder yang digunakan, yang pertama, folder download yang berada di `'{? staging.series ?}'`, `'{? staging.premieres ?}'` dan `'{? staging.movies ?}'`, pastikan path di secrets.yml sesuai.
folder kedua, merupakan lokasi akhir yang menjadi library. Sengaja dipisah agar kodi tidak membaca file - file selain media yang diperlukan, berada di `'{? folder.series ?}'`, `'{? folder.premieres ?}'` dan `'{? folder.movies ?}'`. Sedangkan untuk `'{? media.partition ?}'` adalah folder root dari kedua jenis folder sebelumnya (ini karena saya menggunakan partisi yang sama untuk kedua folder staging dan folder target-nya), flexget akan memantau free space dari partisi ini untuk berhenti seandainya rule free space tercapai di partisi ini.

~~Login di [thetvdb](http://thetvdb.com/) atau register jika belum, masuk ke [account info](http://thetvdb.com/?tab=userinfo) masukkan username dan Account Identifier kedalam secrets.yml di bagian thetvdb. Lalu masuk ke search di site tersebut, cari series yang akan didownload dan jadikan favorit.~~ Kemungkinan thetvdb tidak akan digunakan lagi.

Login / daftar di [pushbullet](https://www.pushbullet.com/) create access token di account settings, masukkan api tersebut ke secrets.yml fungsinya untuk mengirim notifikasi, cukup install applikasi pushbullet dari google play store untuk android.

Untuk telegram, ikuti instruksi [disini](https://core.telegram.org/bots#botfather) untuk membuat bot baru, atau jika sudah, untuk mendapatkan bot-token-nya. `username` disini adalah username dari recipient atau penerima notifikasinya. Jika ini adalah bot baru, buka chat dengan bot tersebut dan click `START` di app telegram.

Login / daftar di [trakt.tv](https://trakt.tv), authorize melalu commandline:

	flexget trakt auth (username yang didaftarkan di trakt.tv)

Ikuti instruksi yang diberikan. Masukan data yang diperlukan di secrets.yml

Gunakan proxy untuk dapat mengakses rss, atau jika sudah menggunakan proxy di OS, skip saja dan hapus baris proxy di config.yml.

Daftar ke <showrss.info> dan buat custom RSS berisi semua tv series yang diikuti.

Dan setelahnya, flexget akan berjalan dibackground secara otomatis, ~~jika ingin menambah series baru cukup tambahkan di thetvdb.com, atau~~ jika ingin secara manual cukup download file .torrent-nya dan simpan di folder `'{? staging.series ?}'`, nanti akan diproses oleh flexget. Jika ingin menambah movie baru cukup search di trakt.tv dan masukkan kedalam watchlist, atau jika ingin secara manual cukup download file .torrent-nya dan simpan di folder `'{? staging.movies ?}'` yang ditentukan di secrets.yml

Tidak ada setting tambahan untuk kodi, cukup masukkan folder tujuan sebagai library didalam kodi, nantinya akan discrape secara otomatis oleh kodi. Add-on yang cukup berguna mungkin subtitle, sudah ada di repo kodi, cukup diinstall saja.

Untuk melihat isi dari transmission, cukup buka browser dan masuk ke http://localhost:9091/ dengan user:pass transmission:transmission.

Jadi untuk alur dari _permission flexget_ ini dibagi berdasarkan _process_, _flexget_ sendiri membutuhkan akses untuk setiap _folder_ yang ditentukan didalam _secrets.yml_. Dan untuk proses _download_ yang diserahkan kepada _transmission_ juga membutuhkan _permission_ berdasarkan dari _transmission_nya, seandainya _flexget_ dijalankan secara global (dengan user khusus), tentunya membutuhkan _permission_ yang berbeda dibanding dengan _flexget_ yang dijalankan menggunakan user biasa.
Note untuk folder `'{? staging.series ?}'`, `'{? staging.premieres ?}'` dan `'{? staging.movies ?}'` perlu diubah permissionnya.

Perintah ini jika menggunakan _transmission_ dengan user yang berbeda:

	sudo usermod -a -G debian-transmission (debian/ubuntu) $USER

	sudo usermod -a -G transmission (distro lainnya) $USER

	sudo chgrp debian-transmission <folder staging series-nya>   # untuk debian/ubuntu

	sudo chgrp transmission <folder staging series-nya>  # untuk distro lain
	
	sudo chgrp debian-transmission <folder staging movies-nya>  # untuk debian/ubuntu

	sudo chgrp transmission <folder staging movies-nya>  # untuk distro lain

	sudo chgrp debian-transmission <folder staging premieres-nya> # untuk debian/ubuntu

	sudo chgrp transmission <folder staging premieres-nya>  # untuk distro lain

	sudo chmod 770 <folder staging series-nya>

	sudo chmod 770 <folder staging premieres-nya>

	sudo chmod 770 <folder staging movies-nya>

Jika ingin menggunakan _transmission_ dengan user yang sama, pastikan saja _permission_ disetiap folder _staging_-nya dapat diakses oleh user yang dipakai.
stop service daemonnya:

	sudo service transmission-daemon stop # debian/ubuntu

dan edit settings.json

	sudo nano /etc/transmission-daemon/settings.json

*Note:* jika tidak ada file `/etc/transmission-daemon/settings.json` bisa coba
cek konfigurasi per-user-nya, biasanya di `~/.config/transmission-daemon/settings.json`.

ubah "umask": 18 menjadi 2 dan rpc-whitelist-enabled menjadi false, lalu start kembali servicenya

	sudo service transmission-daemon start


### Scheduling flexget

Konfigurasi ini saya buat dengan menggunakan scheduling daemon mode, jika
ingin menggunakan metode lain bisa mengacu ke dokumentasi
[flexget](https://flexget.com/InstallWizard/Linux/Scheduling). Konfigurasi
daemon mode ini menggunakan cron, yang dipanggil dengan perintah `crontab
-e`. Biasanya default editor yang digunakan adalah vi, bisa diubah dengan
menambahkan `EDITOR=nano crontab -e`.

Tambahkan baris ini didalam cron:

```
@reboot /usr/local/bin/flexget daemon start -d
```

Pastikan sebelumnya lokasi dari flexget (dengan perintah `which
flexget`). Jika ingin menggunakan file konfigurasi yang berbeda, tambahkan
dengan `-c [path]`

Tapi sebaiknya sebelum memasukkan task cron ini, pastikan juga konfigurasinya
berjalan dengan lancar, bisa di test dengan perintah `flexget --test execute`.
Jika ingin check task tertentu saja, syntax-nya `flexget --test execute --task
'namatasknya'`.

#### Systemd system unit

Konfigurasi global cek [tutorial](https://flexget.com/InstallWizard/Linux/AutoStart) ini.

- install flexget secara global dengan perintah `sudo pip install flexget`.
  Perintah ini akan menginstall flexget di */usr/bin/*.
- Pindahkan isi repository ini ke */etc/flexget*
- Buat unit file flexget dengan nama */usr/lib/systemd/system/flexget.service* dengan isi:
```
[Unit]
Description=Flexget Daemon
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=daemon
Group=daemon
UMask=000
WorkingDirectory=/etc/flexget
ExecStart=/usr/bin/flexget --logfile /var/log/flexget/flexget.log daemon start
ExecStop=/usr/bin/flexget --logfile /var/log/flexget/flexget.log daemon stop
ExecReload=/usr/bin/flexget --logfile /var/log/flexget/flexget.log daemon reload

[Install]
WantedBy=multi-user.target
```
  Pastikan `NetworkManager-wait-online.service` sudah dienable.
- set permission untuk */etc/flexget* dan user daemon
```
sudo mkdir -pv /var/log/flexget
sudo chown daemon:daemon /var/log/flexget
sudo chown daemon:daemon /etc/flexget -Rv
sudo chmod 777 /etc/flexget -Rv
```
- double-check konfigurasi flexget.
- authenticate trakt: `sudo -u daemon flexget -c /etc/flexget/config.yml trakt auth namausertrakt`
- jika sudah sesuai, reload daemon systemd `systemctl daemon-reload` dan enable dan jalankan service-nya dengan perintah `systemctl enable --now flexget-service` 

Tentunya _scheduling_ untuk _transmission-daemon_ juga perlu disesuaikan dengan _scheduling_ flexget yang digunakan. Jika menggunakan user khusus, pastikan _transmission-daemon_ juga dijalankan dengan user yang sesuai.

### Manual Backfill Series

Jika ingin mendownload episode - episode lama, sebelumnya pastikan terlebih dahulu di folder series sudah terisi oleh minimal satu episode dari setiap show yang ingin didownload (berarti flexget telah berjalan sebelumnya dan sudah mencatat semua series yang tersedia di folder). Jalankan perintah ini diterminal `flexget execute --task discover-series-backfill`. Perintah ini akan berjalan sangat lama, terlebih jika banyak series yang ada di folder media. Disarankan matikan dulu scheduler flexget dengan perintah `flexget daemon stop` dan check untuk memastikan tidak ada daemon yang berjalan dengan perintah `flexget daemon status` agar tidak terjadi penumpukan schedule. Untuk saat ini baru rarbg yang digunakan untuk search plugin-nya.

### Proxy

Jika ingin menggunakan proxy, masukkan url proxy di secrets.yml, dan uncomment baris ini (hapus tanda `#`):

```
#    proxy:
#      http: '{? proxy.http ?}'
```

Konfigurasi Proxy
-----

Untuk mudahnya, gunakan [Tor](https://www.torproject.org/) dan Polipo, set proxy http ke 127.0.0.1:8123. Cek [blog ini](https://www.marcus-povey.co.uk/2016/03/24/using-tor-as-a-http-proxy/) untuk info lengkapnya. Dan pastikan cek penggunaan proxy di commandline dengan perintah `http_proxy=$url-proxy curl $url-rss-feed` dengan $url-proxy adalah url proxy yang digunakan (misal seperti di contoh http://127.0.0.1:8123 atau http::/localhost:8123), dan url-rss-feed adalah url dari rss feed yang dipakai didalam file `config.yml`.

Pilihan lain untuk menggunakan Tor adalah dengan mengawali setiap perintah flexget dengan torsocks, misalnya `torsocks flexget execute`. Tapi pastikan baris proxy di config.yml dicomment terlebih dahulu.

Plugins
=======

telegram
--------
Bagian dari _plugin [notifier](https://flexget.com/Plugins/Notifiers)_. Membutuhkan tambahan _package_ *python-telegram-bot*, bisa diinstall melalui _python pip_ atau melalui _package manager_ dari distro yang digunakan. Gunakan instruksi [ini](https://core.telegram.org/bots#6-botfather) untuk membuat bot dan mendapatkan _authorization token_. _Token_ ini yang nantinya dicantumkan didalam file _secret.yml_ beserta _username telegram_ yang akan dikirimkan notifikasi oleh bot tersebut.

![alt text](img/Screenshot_2019-11-27-01-48-58-939_org.thunderdog.challegram.jpg "telegram bot")

