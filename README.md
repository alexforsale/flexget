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

* [flexget](http://flexget.com/)
* transmission-daemon
* transmissionrpc (pip install Transmissionrpc)
* Linux (jelas)
* [kodi](https://kodi.tv/)
* subliminal untuk download subtitle (pip install subliminal)
* telegram bot (pip install python-telegram-bot)

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

Untuk melihat isi dari transmission, cukup buka browser dan masuk ke http://localhost:9091/ dengan user:pass transmission:transmission

Note untuk folder `'{? staging.series ?}'`, `'{? staging.premieres ?}'` dan `'{? staging.movies ?}'` perlu diubah permissionnya.


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


stop service daemonnya

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

Untuk mudahnya, gunakan [Tor](https://www.torproject.org/) dan set proxy http ke 127.0.0.1:8118, cek web mereka untuk informasi lengkapnya.