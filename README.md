# Jarkom-Modul-2-T13-2021

## Anggota Kelompok

| No. | Nama | NRP |
| ------ | ------ | ------ |
| 1. | Heaven Happyna Putra F. | (05311940000026) |
| 2. | Gavin Bagus Kenzie Narain | (05311940000028) |
| 3. | Tera Nurwahyu Pratama | (05311940000039) |

## Daftar Isi
* [Soal 1](#soal-1)
* [Soal 2](#soal-2)
* [Soal 3](#soal-3)
* [Soal 4](#soal-4)
* [Soal 5](#soal-5)
* [Soal 6](#soal-6)
* [Soal 7](#soal-7)
* [Soal 8](#soal-8)
* [Soal 9](#soal-9)
* [Soal 10](#soal-10)
* [Soal 11](#soal-11)
* [Soal 12](#soal-12)
* [Soal 13](#soal-13)
* [Soal 14](#soal-14)
* [Soal 15](#soal-15)
* [Soal 16](#soal-16)
* [Soal 17](#soal-17)

## Soal 1
Luffy adalah seorang yang akan jadi Raja Bajak Laut. Demi membuat Luffy menjadi Raja Bajak Laut, Nami ingin membuat sebuah peta, bantu Nami untuk membuat peta berikut:

![image](https://user-images.githubusercontent.com/73151823/139530926-59de427a-47c8-420d-80c3-ca8934b17d65.png)

EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet.
### Penyelesaian
Pada no 1, kami buat topologi sesuai contoh:
<br>
![image](https://user-images.githubusercontent.com/73151823/139530988-894cbdc2-c804-4884-876f-218902626496.png)
<br>
Untuk router Foosha, konfigurasi nodenya seperti berikut:
<br>
``` 
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.48.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.48.2.1
	netmask 255.255.255.0
```
<br>
Untuk konfigurasi node lainnya seperti berikut:
<br>
<br>
Loguetown
<br>

```
auto eth0
iface eth0 inet static
	address 10.48.1.2
	netmask 255.255.255.0
	gateway 10.48.1.1
  
 ```
 
 Alabasta
<br>
```
auto eth0
iface eth0 inet static
	address 10.48.1.3
	netmask 255.255.255.0
	gateway 10.48.1.1
 ```
 <br>
 
 <br>
 Karena Loguetown dan Alabasta digunakan sebagai client, install dnsutils
 <br>
 
 ```
apt-get update
apt-get install nano -y
apt-get install dnsutils
 ```
 <br>
 
 EniesLobby

```
auto eth0
iface eth0 inet static
	address 10.48.2.2
	netmask 255.255.255.0
	gateway 10.48.2.1
 ```
 
<br>

<br>
EniesLobby sebagai DNS Master

```
apt-get update
apt-get install bind9 -y
apt-get install nano -y
```

<br>

Water7

```
auto eth0
iface eth0 inet static
	address 10.48.2.3
	netmask 255.255.255.0
	gateway 10.48.2.1
 ```
 <br>
 
 <br>
Water7 sebagai DNS Slave

```
apt-get update
apt-get install bind9 -y
apt-get install nano -y
```
 
 Skypie
 
```
auto eth0
iface eth0 inet static
	address 10.48.2.4
	netmask 255.255.255.0
	gateway 10.48.2.1
 ```
 <br>
 
  <br>
Skypie sebagai webserver

```
apt-get update
apt-get install bind9 -y
apt-get install nano -y
apt-get install apache2
apt-get install php
apt-get install libapache2-mod-php7.0
```
 
 Kemudian, mengetikkan ```iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.48.0.0/16``` pada Foosha
 <br>
 Agar setiap node bisa mengakses internet, masukkan ```nameserver 192.168.122.1``` pada ```/etc/resolv/conf```
 
 


## Soal 2
Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku.
### Penyelesaian
#### EniesLobby
gunakan ```nano /etc/bind/named.conf.local``` untuk menambah franky.t13.com pada file konfigurasi
```
zone "franky.t13.com" {
        type master;
        file "/etc/bind/kaizoku/franky.t13.com";
};
```
buat folder baru dengan ```mkdir /etc/bind/kaizoku``` lalu tambahkan file konfigurasi
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.t13.com. root.franky.t13.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.t13.com.
@       IN      A       10.48.2.2
```
restart bind9 ```service bind9 restart```

#### Alabasta/Loguetown
Jangan lupa jalankan install2 pada soal no 1. Jika sudah bisa langsung testing

```ping franky.t13.com```

```ping www.franky.t13.com```

```host -t CNAME www.franky.t13.com```

![image](https://user-images.githubusercontent.com/73151823/139534369-a08e723e-af72-4ff5-b7cb-6f6da561bb0d.png)



## Soal 3
Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie.
### Penyelesaian
#### EniesLobby
menambah 3 line baru pada ```/etc/bind/kaizoku/franky.t13.com```
```
www     IN      CNAME   franky.t13.com.
super   IN      A       10.48.2.4
www.super     IN      CNAME   super.franky.t13.com.
```
#### Alabasta/Loguetown
Jika sudah bisa langsung testing

```ping super.franky.t13.com```

```ping www.super.franky.t13.com```

```host -t CNAME www.super.franky.t13.com```



## Soal 4
Buat juga reverse domain untuk domain utama.
### Penyelesaian


## Soal 5
Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama.
### Penyelesaian

## Soal 6
Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo.
### Penyelesaian

## Soal 7
Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie.
### Penyelesaian

## Soal 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.
### Penyelesaian

## Soal 9
Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home. 
### Penyelesaian

## Soal 10
Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com.
### Penyelesaian

## Soal 11
Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.
### Penyelesaian

## Soal 12
Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache.
### Penyelesaian

## Soal 13
Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js.
### Penyelesaian

## Soal 14
Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500.
### Penyelesaian

## Soal 15
dengan autentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy
### Penyelesaian

## Soal 16
Dan setiap kali mengakses IP Skypie akan dialihkan secara otomatis ke www.franky.yyy.com.
### Penyelesaian

## Soal 17
Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png.
### Penyelesaian

## Kendala yang Dialami
1. Script nya sempat eror
