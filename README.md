Flexget Configuration File
========

Konfigurasi flexget dengan support file secrets. Jelas bukan penggunaan yang sangat "menyeluruh",
tapi cukup sesuai untuk penggunaan pribadi. Cukup menambahkan folder series, Movies, dan anime kedalam library [kodi](https://kodi.tv/)
dan semuanya berjalan dengan (hampir) otomatis.

Note: ini masih work in progress

Saat ini fitur yang berjalan:
* mengimpor list database tv series dari thetvdb.com
* mengimpor list database movies dari trakt.tv
* mengimpor list database anime berdasarkan nama folder (cukup membuat folder baru dengan nama anime yang akan didownload)
* mencari torrent dari rss secara berkala
* memindahkan torrent yang selesai didownload ke folder target
* menghapus list torrent yang selesai didownload dari transmission
* notifikasi melalui pushbullet

Dependencies:
--------

* flexget
* transmission-daemon
* Linux (jelas)
* lupa, sepertinya ada lagi

Note untuk folder /home/{{ user }}/Series, /home/{{ user }}/Movies dan /home/{{ user }}/anime perlu diubah permissionnya.


	sudo usermod -a -G debian-transmission {{ user }}

	sudo chgrp debian-transmission /home/torrent/{{ user }}/Series

	sudo chgrp debian-transmission /home/torrent/{{ user }}/Movies

	sudo chgrp debian-transmission /home/torrent/{{ user }}/anime

	sudo chmod 770 /home/{{ user }}/torrent/Series

	sudo chmod 770 /home/{{ user }}/torrent/Movies

	sudo chmod 770 /home/{{ user }}/torrent/anime

stop service daemonnya

	sudo service transmission-daemon stop

dan edit settings.json

	sudo nano /etc/transmission-daemon/settings.json

ubah "umask": 18 menjadi 2 dan start kembali

	sudo service transmission-daemon start

Jalankan task populate-series-db minimal sekali untuk membaca file yang sudah ada didalam library

	flexget execute --tasks seed-series-db --disable-advancement --learn

minimal sekali setelah set up flexget untuk pertama kalinya, atau jika menambah serie baru yang sudah ada beberapa
didownload

Instalasi
-------

cukup clone ke ~/.flexget

tentunya itu setelah sebelumnya install flexget, informasinya di[sini](http://flexget.com/Install)
