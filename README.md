Flexget Configuration File
========

Konfigurasi flexget dengan support file secrets. Jelas bukan penggunaan yang sangat "menyeluruh",
tapi cukup sesuai untuk penggunaan pribadi. Cukup menambahkan folder series dan movies kedalam library [kodi](https://kodi.tv/)
dan semuanya berjalan dengan (hampir) otomatis.

Note: ini masih work in progress

Saat ini fitur yang berjalan:
* mengimpor list database tv series dari thetvdb.com
* mengimpor list database movies dari trakt.tv
* mencari torrent dari rss secara berkala
* support download manual, cukup taruh file .torrent di /home/{{ user }}/(Series atau Movies) akan otomatis diproses oleh flexget
* download 2 film random per hari dengan rule: imdb rating >7, vote > 800, rilis >2005
* memindahkan torrent yang selesai didownload ke folder target
* menghapus list torrent yang selesai didownload dari transmission
* download subtitle secara otomatis untuk file yang belum memiliki subtitle di library
* notifikasi melalui pushbullet dan telegram

Dependencies:
--------

* [flexget](http://flexget.com/)
* transmission-daemon
* transmissionrpc
* Linux (jelas)
* lupa, sepertinya ada lagi
* [kodi](http://flexget.com/Install)
* subliminal untuk download subtitle (pip install subliminal)
* telegram bot (pip install python-telegram-bot)

Cara penggunaan
-----

install semua yang dibutuhkan seperti kodi, flexget, transmissionrpc dan lainnya, informasi instalasi di [flexget.com](http://flexget.com/Install) sepertinya sudah mengcover semuanya. clone git ini kedalam folder ~/.flexget ubah semua informasi yang di secrets.yml.contoh sesuai kebutuhan, lalu ubah nama filenya menjadi secrets.yml set folder library di kodi sesuai dengan yang dicantumkan di secrets.yml

Ada dua jenis folder yang digunakan, yang pertama, folder download yang berada di /home/{{ user }}, pastikan nama user di secrets.yml sesuai.
folder kedua, merupakan lokasi akhir yang menjadi library. Sengaja dipisah agar kodi tidak membaca file - file selain media yang diperlukan.

Login di [thetvdb](http://thetvdb.com/) atau register jika belum, masuk ke [account info](http://thetvdb.com/?tab=userinfo) masukkan username dan Account Identifier kedalam secrets.yml di bagian thetvdb. Lalu masuk ke search di site tersebut, cari series yang akan didownload dan jadikan favorit.

Login / daftar di [pushbullet](https://www.pushbullet.com/) create access token di account settings, masukkan api tersebut ke secrets.yml fungsinya untuk mengirim notifikasi, cukup install applikasi pushbullet dari google play store untuk android.

Login / daftar di [trakt.tv](https://trakt.tv), authorize melalu commandline:

	flexget trakt auth (username)

ikuti instruksi yang diberikan. Masukan data yang diperlukan di secrets.yml
Dan setelahnya, flexget akan berjalan dibackground secara otomatis, jika ingin menambah series baru cukup tambahkan di thetvdb.com, atau jika ingin secara manual cukup download file .torrent-nya dan simpan di folder /home/{{ user }}/Series nanti akan diproses oleh flexget. Jika ingin menambah movie baru cukup search di trakt.tv dan masukkan kedalam watchlist, atau jika ingin secara manual cukup download file .torrent-nya dan simpan di folder /home/torrent/{{ user }}/Movies.

Tidak ada setting tambahan untuk kodi, cukup masukkan folder tujuan sebagai library didalam kodi, nantinya akan discrape secara otomatis oleh kodi. Add-on yang cukup berguna mungkin subtitle, sudah ada di repo kodi, cukup diinstall saja.

Untuk melihat isi dari transmission, cukup buka browser dan masuk ke http://localhost:9091/ dengan user:pass transmission:transmission

Note untuk folder /home/{{ user }}/torrent/Series dan /home/{{ user }}/torrent/Movies perlu diubah permissionnya.


	sudo usermod -a -G debian-transmission (debian/ubuntu) {{ user }}

	sudo chgrp debian-transmission /home/{{ user }}/torrent/Series

	sudo chgrp debian-transmission /home/{{ user }}/torrent/Movies


	sudo chmod 770 /home/{{ user }}/torrent/Series

	sudo chmod 770 /home/{{ user }}/torrent/Movies


stop service daemonnya

	sudo service transmission-daemon stop

dan edit settings.json

	sudo nano /etc/transmission-daemon/settings.json

ubah "umask": 18 menjadi 2 dan start kembali

	sudo service transmission-daemon start


