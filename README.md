# Jarkom-Modul-3-E10-2021

### Kelompok E10

| **No** | **Nama** | **NRP** | 
| ------------- | ------------- | --------- |
| 1 | Muhammad Rizqi Tsani  | 05111940000045 | 
| 2 | Dicksen Alfersius Novian | 05111940000076 |
| 3 | Bill Harit Yafi | 05111940000114 |



Untuk Soal 3-6, berikut adalah konfigurasi DHCP pada jipangu ```/etc/dhcp/dhcpd.conf```
```bash
#Subnet eth0

subnet 10.34.2.0 netmask 255.255.255.0{
    option routers 10.34.2.1;
}


#Client Melalui Switch 1
subnet 10.34.1.0 netmask 255.255.255.0 {
    range 10.34.1.20 10.34.1.99;
    range 10.34.1.150 10.34.1.169;
    option routers 10.34.1.1;
    option broadcast-address 10.34.1.255;
    option domain-name-servers 10.34.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}

#Client Melalui Switch 3
subnet 10.34.3.0 netmask 255.255.255.0 {
    range 10.34.3.30 10.34.3.50;
    option routers 10.34.3.1;
    option broadcast-address 10.34.3.255;
    option domain-name-servers 10.34.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}

```

## Soal 3

### Soal
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
### Jawaban
Pada jipangu, file konfigurasi DHCP pada ```/etc/dhcp/dhcpd.conf ```
```bash
    range 10.34.1.20 10.34.1.99;
    range 10.34.1.150 10.34.1.169;
```

## Soal 4

### Soal
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 
### Jawaban
Pada jipangu, file konfigurasi DHCP pada ```/etc/dhcp/dhcpd.conf ```
```bash
    range 10.34.3.30 10.34.3.50;
```

## Soal 5

### Soal
Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut
### Jawaban
Pada enies lobby, tambahkan line berikut pada ```/etc/bind/named.conf.options```
```bash

forwarders {
               192.168.122.1;
         };

        //=====================================================================$
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //=====================================================================$
        //dnssec-validation auto;
        allow-query{any;};
```
## Soal 6

### Soal
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.
### Jawaban
Pada jipangu, file konfigurasi DHCP pada ```/etc/dhcp/dhcpd.conf ```

Untuk Switch1, silakan tambah kode berikut 
```bash
    default-lease-time 360;
    max-lease-time 7200;
```

Untuk Switch3, silakan tambah kode berikut 
```bash
    default-lease-time 720;
    max-lease-time 7200;
```


## Soal 12

### Soal
Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps

### Jawaban
Pada server squid, buatlah file konfigurasi baru untuk mengatur bandwidth dan batasan download file. misalkan nama file adalah ```acl-bandwidth.conf```
```bash
# /etc/squid/acl-bandwidth.conf
acl download url_regex -i \.jpg$ \.png$

delay_pools 1
delay_class 1 1
delay_parameters 1 10000/10000
delay_access 1 allow download
delay_access 1 deny all
```

Berikut adalah screenshot testing :

![screenshot_12](https://user-images.githubusercontent.com/65166398/141643855-99b6c64d-cae1-4db8-89cf-a11234930def.png)

## Soal 13

### Soal
Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya

### Jawaban
Untuk soal ini, perlu dibedakan akses antara zoro dan luffy. Oleh karena itu, kita lakukan konfigurasi lebih lanjut pada file ```acl-bandwidth.conf``` sehingga sesuai permintaan soal.
```bash
acl download url_regex -i \.jpg$ \.png$
acl download-rev url_regex -i \.js$ \.css$ \.99689$ \.cursed$ \.listingbro$ \.woi$ \.html$

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd

acl luffy proxy_auth luffybelikapale10
acl zoro proxy_auth zorobelikapale10

delay_pools 2
delay_class 1 1
delay_parameters 1 10000/10000
delay_access 1 allow luffy
delay_access 1 allow download
delay_access 1 deny all

delay_class 2 1
delay_parameters 2 -1/-1
delay_access 2 allow zoro
delay_access 2 deny all

http_access deny zoro download
http_access deny luffy download-rev

```
Berikut adalah testing untuk user 'zorobelikapale10'. User tersebut tidak bisa mengunduh file gambar .png dan .jpg
![screenshot_13](https://user-images.githubusercontent.com/65166398/141643947-93cade63-aad0-469e-92ae-b0776fbccd8c.png)



## Kendala
- Materi DHCP Relay yang tidak terdapat pada modul
- Waktu praktikum yang kurang panjang
