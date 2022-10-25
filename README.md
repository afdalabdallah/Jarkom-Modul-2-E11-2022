<table>
<tr>
<th>Nama</th>
<th>NRP </th>
</tr>
<tr>
<td>Surya Abdillah</td>
<td>5025201229 </td>
</tr>
<tr>
<td>Muhammad Afdal Abdallah</td>
<td>5025201163 </td>
</tr>
<tr>
<td>Kadek Ari Dharmika</td>
<td>5025201239 </td>
</tr>
</table>

# Modul 2

<img width="527" alt="image" src="https://user-images.githubusercontent.com/103357229/197660119-4a6819f9-a539-46ae-a604-fc311a604e6f.png">

## Pembuatan Topologi dan Konfigurasi
> Topologi dapat dibuat dengan melakukan modifikasi pada topologi dari latihan yang ada pada mmodul GNS3

<img width="939" alt="image" src="https://user-images.githubusercontent.com/103357229/197660362-c69594bb-c35b-4b3e-a71d-5d79e93618ca.png">

### Melakukan konfigurasi pada router `Ostania`
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.27.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.27.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.27.3.1
	netmask 255.255.255.0
```

### Melakukan konfigurasi pada node-node lain
1. Node SSS
```
auto eth0
iface eth0 inet static
	address 10.27.1.2
	netmask 255.255.255.0
	gateway 10.27.1.1
```
2. Node Garden
```
auto eth0
iface eth0 inet static
	address 10.27.1.3
	netmask 255.255.255.0
	gateway 10.27.1.1
```
3. Node WISE
```
auto eth0
iface eth0 inet static
	address 10.27.3.2
	netmask 255.255.255.0
	gateway 10.27.3.1
```
4. Node Berlint
```
auto eth0
iface eth0 inet static
	address 10.27.2.2
	netmask 255.255.255.0
	gateway 10.27.2.1
```
5. Node Eden
```
auto eth0
iface eth0 inet static
	address 10.27.2.3
	netmask 255.255.255.0
	gateway 10.27.2.1
```

#### Melakukan IPtables pada router
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.27.0.0/16
cat /etc/resolv.conf
```
> Didapatkan IP `192.168.122.1`, lakukan perubahan pada `/etc/resolv.conf` di setiap node-node dengan kode berikut:
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```
> Setelah melakukan step di atas, maka setiap node sudah dapat dilakukan ping ke google `ping google.com -c 5`

## SOAL PRAKTIKUM
### NOMOR 1
> WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet (1).
#### Instalansi bind
> Lakukan kode berikut pada `WISE`
```
apt-get update
apt-get install bind9 -y
```
#### Pembuatan Domain
##### konfigurasi pada WISE
> Lakukan kode berikut pada `WISE`, membuka file `nano /etc/bind/named.conf.local` dan mengubahnya menjadi kode berikut
```
zone "wise.e11.com" {
	type master;
	file "/etc/bind/wise/wise.e11.com";
};
```

> membuat folder `/etc/bind/wise` dan mengcopy
```
mkdir /etc/bind/wise
cp /etc/bind/db.local /etc/bind/wise/wise.e11.com
```

> mengedit file `nano /etc/bind/wise/wise.e11.com`

![messageImage_1666685362495](https://user-images.githubusercontent.com/103357229/197719660-397e34a4-3eb0-46a2-87ca-f440164d5a74.jpg)

> restart bind
```
service bind9 restart
```

> merubah `nano /etc/bind/named.conf.local`

![messageImage_1666685776569](https://user-images.githubusercontent.com/103357229/197720911-f285e6f1-968a-4411-998d-403229ea10d5.jpg)

##### Konfigurasi pada Berlint
```
apt-get update
apt-get install bind9 -y
```

> mengedit file `/etc/bind/named.conf.local`

![messageImage_1666686296184](https://user-images.githubusercontent.com/103357229/197722867-90462499-2453-457e-ae1a-2db13bb128ec.jpg)

```
service bind9 restart
```
#### Testing
> `nano /etc/resolv.conf` pada SSS
```
nameserver 10.27.3.2
nameserver 10.27.2.2
```

![messageImage_1666686508595](https://user-images.githubusercontent.com/103357229/197723807-fec87009-ada0-4465-a0b4-4f0f7c97e3e0.jpg)

### NOMOR 2
> Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise (2).

#### pada WISE
![messageImage_1666686673669](https://user-images.githubusercontent.com/103357229/197724294-43c9b9b8-cf59-4a9d-9ac4-b541d80643c7.jpg)

#### testing 
![messageImage_1666686788246](https://user-images.githubusercontent.com/103357229/197724732-93270225-47b6-4639-8ff3-8a2fe22b131b.jpg)

### NOMOR 3
> Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden (3).

#### Konfigurasi WISE SUB DOMAIN
![messageImage_1666687200780](https://user-images.githubusercontent.com/103357229/197726364-2ce52767-b07e-41b2-a36b-399000b1df52.jpg)

![messageImage_1666687289381](https://user-images.githubusercontent.com/103357229/197726824-2d8e10fd-998f-43c0-a46e-711819d50b1a.jpg)

#### Konfigurasi WISE CNAME
![messageImage_1666687852004](https://user-images.githubusercontent.com/103357229/197728970-b42f5f98-7928-43de-966b-4f7dc88fb3a3.jpg)

![messageImage_1666687857673](https://user-images.githubusercontent.com/103357229/197729028-496dbc96-ff1c-4b79-a641-a7363cab2a16.jpg)

### NOMOR 4
> Buat juga reverse domain untuk domain utama (4).

```
cp /etc/bind/db.local /etc/bind/wise/3.27.10.in-addr.arpa
```

![messageImage_1666688213843](https://user-images.githubusercontent.com/103357229/197730446-be8b4e07-269a-453d-b586-933e04a6bd10.jpg)

#### SSS
```
nano /etc/resolv.conf
```
tambahkan `nameserver 192.168.122.1`
```
apt-get update
apt-get install dnsutils
```

> menghapus `nameserver 192.168.122.1`

#### testing
```
host -t PTR 10.27.3.2
```

![messageImage_1666688680590](https://user-images.githubusercontent.com/103357229/197732227-4f58d62e-a032-42a5-83b8-6bad2270d9d7.jpg)

### NOMOR 5
> Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama (5).

> telah dijalankan pada nomor 1

### NOMOR 6
> Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation (6).

> Subdomain
![messageImage_1666689417603](https://user-images.githubusercontent.com/103357229/197735103-7f544844-202f-41b1-b57d-c0c0595272aa.jpg)

> CNAME
![messageImage_1666689510230](https://user-images.githubusercontent.com/103357229/197735421-b4ca49fc-cffa-48d5-808e-cab43f528436.jpg)
```
nameserver 10.27.3.2
nameserver 10.27.2.2
```
![messageImage_1666689515387](https://user-images.githubusercontent.com/103357229/197735458-e35e7356-7203-4db8-b7a2-22975be90d0d.jpg)

> BERLINT
![messageImage_1666690457633](https://user-images.githubusercontent.com/103357229/197738870-4433d5c1-3405-45aa-9f4c-0c364a9f79b5.jpg)
![messageImage_1666697899895](https://user-images.githubusercontent.com/103357229/197764792-9c91e2fd-2206-47c0-b36b-199a43c96c9f.jpg)
![messageImage_1666697861899](https://user-images.githubusercontent.com/103357229/197764799-5a28f084-c3c7-4084-8fea-ae4d4de3fe71.jpg)
![messageImage_1666697843155](https://user-images.githubusercontent.com/103357229/197764806-6e253dc1-6415-40d5-b335-9c21d23cf20e.jpg)

> WISE
![messageImage_1666697716806](https://user-images.githubusercontent.com/103357229/197765000-a1fb0c79-b6af-44ca-866a-1c4cc2c8550e.jpg)
![messageImage_1666697799957](https://user-images.githubusercontent.com/103357229/197765011-6dee52ef-b4fb-4c8f-90ce-60b2ab7267f7.jpg)
![messageImage_1666697741886](https://user-images.githubusercontent.com/103357229/197765026-6d4f496b-6714-443f-8245-9ac635311aef.jpg)

> Testing
![messageImage_1666697928574](https://user-images.githubusercontent.com/103357229/197765177-22205f18-3128-4ef7-9f4d-663af180ac4c.jpg)
![messageImage_1666697953890](https://user-images.githubusercontent.com/103357229/197765185-45fcb33b-ac82-450a-bb86-d36d7c0b575b.jpg)


### NOMOR 7
> Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden (7).

### NOMOR 8
> Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com (8).

### NOMOR 9
> Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home (9).

### NOMOR 10
> Setelah itu, pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com (10).

### NOMOR 11
> Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja (11).

### NOMOR 12
> Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache (12).

### NOMOR 13
> Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js (13).

### NOMOR 14
> Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500 (14)

### NOMOR 15
> dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy (15)

### NOMOR 16
> dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com (16).

### NOMOR 17
> Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian! (17)
