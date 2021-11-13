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

## Soal 10
Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)

## Soal 11
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie  
