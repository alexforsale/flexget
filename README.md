Flexget Configuration File
========

konfigurasi flexget dengan support file secrets.

note untuk folder /home/{{ user }}/Series, /home/{{ user }}/Movies dan /home/{{ user }}/anime perlu diubah permissionnya


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

