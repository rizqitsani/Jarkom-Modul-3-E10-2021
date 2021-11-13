# Jarkom-Modul-3-E10-2021

### Kelompok E10

| **No** | **Nama** | **NRP** | 
| ------------- | ------------- | --------- |
| 1 | Muhammad Rizqi Tsani  | 05111940000045 | 
| 2 | Dicksen Alfersius Novian | 05111940000076 |
| 3 | Bill Harit Yafi | 05111940000114 |

## Soal 1

### Soal
Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server

### Jawaban

```bash
# Jipangu
apt-get update
apt-get install isc-dhcp-server -y

cp -r shift3/default /etc/
cp -r shift3/dhcp /etc/

service isc-dhcp-server start

# Water7
apt-get update
apt-get install squid -y

# EniesLobby
apt-get update
apt-get install bind9 -y
```

Lalu pada Jipangu, ubah config interface di isc-dhcp-server menjadi eth0

```bash
INTERFACES="eth0"
```

## Soal 2

### Soal
Foosha sebagai DHCP Relay

### Jawaban

Install relay pada Foosha

```bash
apt-get install isc-dhcp-relay -y
```

Lalu ubah config pada relay /etc/default/isc-dhcp-relay menjadi

```bash
# What servers should the DHCP relay forward requests to?
SERVERS="10.34.2.4"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

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
Pada jipangu, file konfigurasi DHCP pada `/etc/dhcp/dhcpd.conf `

```bash
range 10.34.1.20 10.34.1.99;
range 10.34.1.150 10.34.1.169;
```

## Soal 4

### Soal
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 
### Jawaban
Pada jipangu, file konfigurasi DHCP pada `/etc/dhcp/dhcpd.conf `

```bash
range 10.34.3.30 10.34.3.50;
```

## Soal 5

### Soal
Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut
### Jawaban
Pada enies lobby, tambahkan line berikut pada `/etc/bind/named.conf.options`

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
Pada jipangu, file konfigurasi DHCP pada `/etc/dhcp/dhcpd.conf `

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

## Soal 7

### Soal
Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69

### Jawaban

Pertama, dapatkan hwaddress Skypie (`6a:41:b2:2c:ae:8b`)

![hwAddress_DHCP](https://user-images.githubusercontent.com/68275535/141614807-7a7f99c5-bf96-43e7-95e8-ab50ae6f79fc.jpg)

Kemudian config pada Jipangu, dhcpd.conf

```bash
#fixed address Skypie
host Skypie{
    hardware ethernet 6a:41:b2:2c:ae:8b;
    fixed-address 10.34.3.69;
}
```

Konfigurasi interface Skypie

```bash
auto eth0
iface eth0 inet dhcp
hwaddress ether 6a:41:b2:2c:ae:8b
```

![Interface_Skypie](https://user-images.githubusercontent.com/68275535/141614824-2806ccf1-2789-48e4-b0b2-d93979802a2c.jpg)

Test IP Static

![StaticIP_Skypie](https://user-images.githubusercontent.com/68275535/141614841-10ddac58-2aa7-45cf-a161-40645c060234.jpg)

## Soal 8

### Soal
Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi.

Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000

### Jawaban

Pada squid.conf di Water7, tambahkan

```bash
http_port 5000
visible_hostname jualbelikapal.e10.com
```

Lalu nyalakan squid

Untuk pengujian, lakukan instalasi lynx dan tambahkan perintah sebagai berikut

```bash
apt-get update
apt-get install -y  lynx
export http_proxy=http://10.34.2.3:5000
```

Coba perintah

`lynx http://its.ac.id`

![proxy_lynx_denied](https://user-images.githubusercontent.com/68275535/141614852-29c0198e-1ae2-49eb-ad10-4c718334e812.jpg)

Kemudian ditambahkan perintah pada /etc/squid/squid.conf agar dapat mengakses http

```bash
http_access allow all
```

![lynx_success](https://user-images.githubusercontent.com/68275535/141614861-55e5374e-b852-4d73-b159-8433cf39325d.jpg)

## Soal 9

### Soal
Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy

### Jawaban

Pada Water7, tambahkan perintah berikut

```bash
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

Lalu tambah config squid

```bash
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

Untuk pengujian, jalankan
`lynx http://its.ac.id`

![lynx_password](https://user-images.githubusercontent.com/68275535/141614870-2ca58464-8bf1-4e3e-b1b0-e205bb3c5e9a.jpg)

## Soal 10

### Soal
Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)

### Jawaban

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

![image](https://user-images.githubusercontent.com/68275535/141646451-cbf8fcad-dd57-4067-93c6-081412c03290.png)

## Soal 11

### Soal
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie

### Jawaban

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
