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

#### Tangkapan Layar hasil ping
![image](https://user-images.githubusercontent.com/90978855/198832990-ffaed1f5-e227-4a9a-925c-d138a0974a86.png)

![image](https://user-images.githubusercontent.com/90978855/198832994-b6df6c19-988b-48aa-afad-52c75099613c.png)

![image](https://user-images.githubusercontent.com/90978855/198832997-d5ac0a1d-b6a2-426a-b740-6c6267123cb5.png)

![image](https://user-images.githubusercontent.com/90978855/198833001-e2096974-c833-4983-96d4-53f032fac520.png)

![image](https://user-images.githubusercontent.com/90978855/198833008-db596255-744a-41c6-bcfa-e3c6c44d8865.png)

![image](https://user-images.githubusercontent.com/90978855/198833013-3c18dd55-6931-46d4-8ae0-691b144ecaef.png)

### NOMOR 2
> Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise (2).

#### pada WISE
> Mengedit file wise.e11.com dengan membuat domain dan alias (CNAME) 

```
echo "
\$TTL    604800
@       IN      SOA     wise.e11.com. root.wise.e11.com. (
                        2022102501       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      wise.e11.com.
@               IN      A       10.27.3.2 ; IP WISE
www             IN      CNAME   wise.e11.com.
@               IN      AAAA    ::1
" > /etc/bind/wise/wise.e11.com
service bind9 restart
```

![messageImage_1666686673669](https://user-images.githubusercontent.com/103357229/197724294-43c9b9b8-cf59-4a9d-9ac4-b541d80643c7.jpg)

#### testing 
> Merubah nameserver pada SSS dan Garden dengan IP "10.27.3.2"

```
echo 'nameserver 10.27.3.2' > /etc/resolv.conf
```

> Hasil Ping 

![messageImage_1666686788246](https://user-images.githubusercontent.com/103357229/197724732-93270225-47b6-4639-8ff3-8a2fe22b131b.jpg)

### NOMOR 3
> Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden (3).

#### Konfigurasi WISE SUB DOMAIN
> Menambahkan konfigurasi pada WISE dengan menambahkan subdomain eden dan CNAME-nya

```
eden            IN      A       10.27.2.3 ; IP Eden
www.eden        IN      CNAME   eden.wise.e11.com.
```

#### Testing
> Lakukan restart bind9 pada WISE

```
service bind9 restart
```

> PING eden dan www.eden pada client

![image](https://user-images.githubusercontent.com/90978855/198833412-41936e7d-d171-40e4-a1dd-875d7e066844.png)

![image](https://user-images.githubusercontent.com/90978855/198833417-ce4fbc74-d981-4145-b862-8bcf9065527f.png)


### NOMOR 4
> Buat juga reverse domain untuk domain utama (4).

#### Konfigurasi pada WISE

> Mengedit file `/etc/bind/wise/3.27.10.in-addr.arpa`

```
cp /etc/bind/db.local /etc/bind/wise/3.27.10.in-addr.arpa
echo "
\$TTL    604800
@       IN      SOA     wise.e11.com. root.wise.e11.com. (
                        2022102501       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
3.27.10.in-addr.arpa.   IN      NS      wise.e11.com.
2                       IN      PTR     wise.e11.com.
" > /etc/bind/wise/3.27.10.in-addr.arpa
```

> Mengedit file `/etc/bind/named.conf.local`

```
echo '
zone "wise.e11.com" {
    type master;
    notify yes;
    also-notify { 10.27.2.2; };
    allow-transfer{ 10.27.2.2; };
    file "/etc/bind/wise/wise.e11.com";
};

zone "3.27.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/3.27.10.in-addr.arpa";
};' > /etc/bind/named.conf.local
```

> Melakukan restart bind9

```
service bind9 restart
```

#### TESTING

![image](https://user-images.githubusercontent.com/90978855/198833625-f5a1fd3f-2186-413c-b227-0110d2bdc7c5.png)

### NOMOR 5
> Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama (5).

#### WISE

> mengedit file `named.conf.local`

```
echo '
zone "wise.e11.com" {
        type master;
        notify yes;
        also-notify {10.27.2.2;};  //Masukan IP Berlint tanpa tanda petik
        allow-transfer {10.27.2.2;}; // Masukan IP Berlint tanpa tanda petik
        file "/etc/bind/wise/wise.e11.com";
};

zone "3.27.10.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/3.27.10.in-addr.arpa";
};' > /etc/bind/named.conf.local
```

> Restart bind9

```
service bind9 restart
```

##### Berlint

> merubah nameserver untuk install

```
echo '
nameserver 192.168.122.1
' > /etc/resolv.conf
```

> Install bind9

```
apt-get update
apt-get install bind9 -y
```

> Mengedit file `named.conf.local`

```
echo '
zone "wise.e11.com" {
    type slave;
    masters { 10.27.3.2; };
    file "/var/lib/bind/wise.e11.com";
};
' > /etc/bind/named.conf.local
```

> Mengembalikan nameserver dan melakukan restart bind9

```
echo '
nameserver 10.27.3.2
' > /etc/resolv.conf

service bind9 restart
```

#### Testing

> Pada wise dilakukan stop dengan 

```
service bind9 stop
```

> Melakukan ping wise dan eden pada clients

![image](https://user-images.githubusercontent.com/90978855/198833888-abbb26c4-af51-4cb7-ae57-50b87838cfe6.png)

![image](https://user-images.githubusercontent.com/90978855/198833907-bb407506-a61f-4e6b-8e94-7e1a1b31e3c9.png)

### NOMOR 6
> Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation (6).

#### WISE

> Mengedit file `wise.e11.com`

```
echo "
\$TTL    604800
@       IN      SOA     wise.e11.com. root.wise.e11.com. (
                        2022102501       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      wise.e11.com.
@               IN      A       10.27.3.2 ; IP WISE
www             IN      CNAME   wise.e11.com.
eden            IN      A       10.27.2.3 ; IP Eden
www.eden        IN      CNAME   eden.wise.e11.com.
ns1             IN      A       10.27.2.2  ; IP Berlint
operation       IN      NS      ns1
@               IN      AAAA    ::1
" > /etc/bind/wise/wise.e11.com
```

> Mengedit file `named.conf.local`, lalu restart bind9

```
echo 'options {
        directory "/var/cache/bind";
        // forwarders {
        //      0.0.0.0;
        // };
        //dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
service bind9 restart
```

#### Berlint

> Membuat dan mengedit file `named.conf.local`

```
cp /etc/bind/db.local /etc/bind/operation/operation.wise.e11.com
echo '
zone "wise.e11.com" {
    type slave;
    masters { 10.27.3.2; }; // IP WISE
    file "/var/lib/bind/wise.e11.com";
};

zone "operation.wise.e11.com" {
    type master;
    file "/etc/bind/operation/operation.wise.e11.com";
};
' > /etc/bind/named.conf.local
```

> Mengedit file `operation.wise.e11.com`

```
echo "
\$TTL    604800
@       IN      SOA     operation.wise.e11.com. root.operation.wise.e11.com. (
                        2022102501       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      operation.wise.e11.com.
@               IN      A       10.27.2.3 ; IP Eden
www             IN      CNAME   operation.wise.e11.com.  ;
" > /etc/bind/operation/operation.wise.e11.com
```

> Mengedit file `named.conf.option` dan restart bind9

```
echo 'options {
        directory "/var/cache/bind";
        // forwarders {
        //      0.0.0.0;
        // };
        //dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
service bind9 restart
```

#### Testing

![image](https://user-images.githubusercontent.com/90978855/198834276-cb0789f1-4c11-4e7a-abf1-a35febb2dde1.png)

![image](https://user-images.githubusercontent.com/90978855/198834279-c19526a1-4bc6-4533-bc05-be7ac6903e7c.png)
 
### NOMOR 7
> Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden (7).

> Mengedit file `opeartion.wise.e11.com` dan restart bind9

```
echo "
\$TTL    604800
@       IN      SOA     operation.wise.e11.com. root.operation.wise.e11.com. (
                        2022102601       ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      operation.wise.e11.com.
@               IN      A       10.27.2.3 ; IP Eden
www             IN      CNAME   operation.wise.e11.com.  ; IP Berlint
strix           IN      A       10.27.2.3   ; IP Eden
www.strix       IN      CNAME   strix.operation.wise.e11.com.
" > /etc/bind/operation/operation.wise.e11.com

service bind9 restart
```

#### Testing

![image](https://user-images.githubusercontent.com/90978855/198834536-50c54b75-6128-4f59-a78d-ea4d4f1ec25a.png)

![image](https://user-images.githubusercontent.com/90978855/198834542-0fcdc12c-f938-488a-93bf-c38590fae8a7.png)

### NOMOR 8
> Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com (8).

![image](https://user-images.githubusercontent.com/90848018/198838425-48072ac7-f7a4-4d86-8963-afd23bc2d6b6.png)

![image](https://user-images.githubusercontent.com/90848018/198838341-10384f17-ccf5-4c77-9428-d38cf736d7c8.png)

![image](https://user-images.githubusercontent.com/90848018/198838325-410dc440-f59d-45bd-b533-c6b609e9e224.png)

### NOMOR 9
> Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home (9).

![image](https://user-images.githubusercontent.com/90848018/198838425-48072ac7-f7a4-4d86-8963-afd23bc2d6b6.png)

![image](https://user-images.githubusercontent.com/90848018/198838393-d9b16819-e5de-4687-a96c-1e5a4b1e9894.png)

### NOMOR 10
> Setelah itu, pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com (10).

![image](https://user-images.githubusercontent.com/90848018/198838542-daf72425-e1f9-4d39-858c-6025213cc73e.png)

![image](https://user-images.githubusercontent.com/90848018/198838518-ad9fc63c-ab13-4d32-87ac-b03c3a86c9c3.png)

### NOMOR 11
> Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja (11).

![image](https://user-images.githubusercontent.com/90848018/198838630-f34e8e50-a116-443d-a7de-ac981a9e714f.png)

### NOMOR 12
> Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache (12).

![image](https://user-images.githubusercontent.com/90848018/198838663-51af35d5-ef02-406d-94e3-8646e0de232a.png)

### NOMOR 13
> Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js (13).

![image](https://user-images.githubusercontent.com/90848018/198838691-7ce800df-035c-445b-9f05-99153df66bd6.png)

### NOMOR 14
> Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500 (14)

### NOMOR 15
> dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy (15)

### NOMOR 16
> dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com (16).

### NOMOR 17
> Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian! (17)
