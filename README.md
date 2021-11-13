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
# Jarkom-Modul-3-E10-2021

## Soal 8
Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi. Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000

Pada squid.conf di water7, tambahkan
```
http_port 5000
visible_hostname jualbelikapal.e10.com
```
Kemudian Nyalakan Squid

### Testing 
Lakukan instalasi lynx dan tambahkan perintah sebagai berikut
```
apt-get update
apt-get install -y  lynx
export http_proxy=http://10.34.2.3:5000
```
Coba perintah `lynx http://its.ac.id`

![image](https://user-images.githubusercontent.com/77628684/141643103-4bd0b977-284b-4f2e-b6e7-cf8c8ce1110c.png)

Kemudian ditambahkan perintah pada /etc/squid/squid.conf agar dapat mengakses http

```
http_access allow all
```

![image](https://user-images.githubusercontent.com/77628684/141643120-3aa62ff4-efb4-4dde-8428-3734d8da6e9a.png)


## Soal 9
agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy

Pada water7, tambahkan perintah berikut

```
apt-get update
apt-get install -y apache2-utils 

touch /etc/squid/passwd

htpasswd -m /etc/squid/passwd luffybelikapale10
luffy_e10
luffy_e10

htpasswd -m /etc/squid/passwd zorobelikapale10
zoro_e10
zoro_e10
```

Kemudian tambahkan config squidnya

```
http_port 5000
visible_hostname jualbelikapal.e10.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
```
### Testing
`lynx http://its.ac.id`

![image](https://user-images.githubusercontent.com/77628684/141644565-9269aa27-d6b6-4b4f-a554-98411911f1b7.png)


## Soal 10
Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)

```bash
# /etc/squid/acl.conf

acl AVAILABLE_WORKING_1 time MTWH 07:00-11:00
acl AVAILABLE_WORKING_2 time TWHF 17:00-24:00
acl AVAILABLE_WORKING_3 time WHFA 00:00-03:00

```

Tambahkan perintah pada etc/squid/squid.conf

```bash
include /etc/squid/acl.conf

http_port 5000
visible_hostname jualbelikapal.e10.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS AVAILABLE_WORKING_1
http_access allow USERS AVAILABLE_WORKING_2
http_access allow USERS AVAILABLE_WORKING_3
```

## Soal 11
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie  

Pada EniesLobby, lakukan config DNS

```bash
# /etc/bind/named.conf.local

zone "super.franky.e10.com" {
    type master;
    file "/etc/bind/kaizoku/super.franky.e10.com";
    allow-transfer { 10.34.3.69; }; // Masukan IP Skypie tanpa tanda petik
};
```

```bash
# /etc/bind/kaizoku/super.franky.e10.com
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     super.franky.e10.com. root.super.franky.e10.com. (
                        2021110801      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      super.franky.e10.com.
@       IN      A       10.34.3.69
www     IN      CNAME   super.franky.e10.com.
```

Lalu tambahkan file html ke /var/www/super.franky.e10.com pada **Skypie**

```bash
# /etc/apache2/apache2.conf
...

ServerName 10.34.3.69
```

```bash
# /etc/apache2/sites-available/super.franky.e10.com.conf
<VirtualHost *:80>
    ServerName super.franky.e10.com
    ServerAlias www.super.franky.e10.com

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/super.franky.e10.com

    <Directory /var/www/super.franky.e10.com>
        Options +Indexes
        AllowOverride All
    </Directory>

    <Directory /var/www/super.franky.e10.com/public>
        Options +Indexes
    </Directory>

    Alias "/js" "/var/www/super.franky.e10.com/public/js"

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    ErrorDocument 404 /error/404.html
</VirtualHost>
```

Setelah selesai melakukan config untuk DNS dan apache, maka kita bisa melakukan pengujian untuk super franky

`lynx http://super.franky.e10.com`

![superFranky_lynx](https://user-images.githubusercontent.com/68275535/141614883-4ec6ab6b-d332-433e-b804-b7def2c2d3d3.jpg)

Lalu untuk redirect google.com

```bash
http_port 5000
visible_hostname jualbelikapal.e10.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIREDqqq
http_access allow USERS AVAILABLE_WORKING_1
http_access allow USERS AVAILABLE_WORKING_2
http_access allow USERS AVAILABLE_WORKING_3

acl BLACKLIST dstdomain google.com
deny_info http://super.franky.e10.com/ BLACKLIST
http_reply_access deny BLACKLIST
```

Water7 → jangan lupa ganti nameserver ke enieslobby

`lynx google.com`

![lynx_googleToFranky](https://user-images.githubusercontent.com/68275535/141614902-8a8103c9-418a-4239-b4aa-c4d07c1cb7d1.jpg)

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
