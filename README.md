# Jarkom-Modul-5-D02-2022

## Anggota Kelompok D02
| NRP | Nama |
| :---:        |     :---:           |
| 5025201082   | Farrel Emerson      | 
| 5025201087   | Daniel Hermawan     |      
| 5025201003   | Rahmat Faris Akbar  |     

# Soal dan Jawaban

## (A) Topologi
![image](https://user-images.githubusercontent.com/99629909/206746808-40e129e8-671f-4b54-92e2-2f44d2e78e3f.png)

## Keterangan Node:

- Eden adalah DNS Server
- WISE adalah DHCP Server
- Garden dan SSS adalah Web Server
- Jumlah Host pada Forger adalah 62 host
- Jumlah Host pada Desmond adalah 700 host
- Jumlah Host pada Blackbell adalah 255 host
- Jumlah Host pada Briar adalah 200 host

## (B) Pembagian Subnet - VLSM

![Subnet VLSM](https://cdn.discordapp.com/attachments/856609726225973278/1049249641172062239/Subnet_VLSM.png)

## Penghitungan Jumlah Subnet

![image](https://user-images.githubusercontent.com/99629909/206746981-db41638e-5086-4767-ae4b-79c0da433cfb.png)

## VLSM Tree

![image](https://user-images.githubusercontent.com/99629909/206747135-60912d67-77f3-4d04-90c9-fbeb291b85ec.png)

## Pembagian IP

![image](https://user-images.githubusercontent.com/99629909/206747237-60363daa-6065-4e08-9498-ebbc4989e320.png)

## Network Configuration

### Strix

```
auto eth0
iface eth0 inet dhcp
hwaddress ether 3a:20:6d:d7:5b:79
auto eth1
iface eth1 inet static
        address 10.16.0.1
        netmask 255.255.255.252
auto eth2
iface eth2 inet static
        address 10.16.0.5
        netmask 255.255.255.252
```
**Strix** menggunakan `hwaddress` agar bisa mendapatkan fixed address yang akan digunakan untuk konfigurasi iptables nantinya. Fixed address dari **Strix** adalah `192.168.122.227`

### Westalis

```
auto eth0
iface eth0 inet static
        address 10.16.0.2
        netmask 255.255.255.252
        gateway 10.16.0.1
auto eth1
iface eth1 inet static
        address 10.16.0.17
        netmask 255.255.255.248
auto eth2
iface eth2 inet static
        address 10.16.0.129
        netmask 255.255.255.128
auto eth3
iface eth3 inet static
        address 10.16.4.1
        netmask 255.255.252.0
```

### Ostania

```
auto eth0
iface eth0 inet static
        address 10.16.0.6
        netmask 255.255.255.252
        gateway 10.16.0.5
auto eth1
iface eth1 inet static
        address 10.16.1.1
        netmask 255.255.255.0
auto eth2
iface eth2 inet static
        address 10.16.0.25
        netmask 255.255.255.248
auto eth3
iface eth3 inet static
        address 10.16.2.1
        netmask 255.255.254.0
```

### Eden

```
auto eth0
iface eth0 inet static
        address 10.16.0.18
        netmask 255.255.255.248
        gateway 10.16.0.17
```

### WISE

```
auto eth0
iface eth0 inet static
        address 10.16.0.19
        netmask 255.255.255.248
        gateway 10.16.0.17
```

### SSS

```
auto eth0
iface eth0 inet static
        address 10.16.0.26
        netmask 255.255.255.248
        gateway 10.16.0.25
```

### Garden

```
auto eth0
iface eth0 inet static
        address 10.16.0.27
        netmask 255.255.255.248
        gateway 10.16.0.25
```

## (C) Routing

Pada Strix

```
route add -net 10.16.0.16 netmask 255.255.255.248 gw 10.16.0.2
route add -net 10.16.0.128 netmask 255.255.255.128 gw 10.16.0.2
route add -net 10.16.4.0 netmask 255.255.252.0 gw 10.16.0.2
route add -net 10.16.1.0 netmask 255.255.255.0 gw 10.16.0.6
route add -net 10.16.0.24 netmask 255.255.255.248 gw 10.16.0.6
route add -net 10.16.2.0 netmask 255.255.254.0 gw 10.16.0.6
```

Pada Westalis

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.16.0.1
```

Pada Ostania

```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.16.0.5
```

## IP Tables Strix
Pada router Strix jalankan perintah berikut ini (belum menjawab soal 1):
```
  iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.16.0.0/21
```

## Setting resolv.conf
Pada semua node selain Strix (termasuk router-router lain), jalankan perintah berikut ini:
```
  echo nameserver 192.168.122.1 > /etc/resolv.conf
```

## (D) DHCP

Pada **Forger**, **Desmond**, **Blackbell**, dan **Briar** dilakukan network configuration seperti berikut

```
auto eth0
iface eth0 inet dhcp
```

### DHCP Server

1. Pada **WISE** sebagai DHCP Server, dilakukan instalasi DHCP Server

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```

2. Konfigurasi file `/etc/default/isc-dhcp-server` pada **WISE** sebagai DHCP Server

```
INTERFACES="eth0"
```

3. Konfigurasi file `/etc/dhcp/dhcpd.conf` pada **WISE** sebagai DHCP Server

```
# Forger (A2)
subnet 10.16.0.128 netmask 255.255.255.128 {
        range 10.16.0.130 10.16.0.254;
        option routers 10.16.0.129;
        option broadcast-address 10.16.0.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# Desmond (A3)
subnet 10.16.4.0 netmask 255.255.252.0 {
        range 10.16.4.2 10.16.7.254;
        option routers 10.16.4.1;
        option broadcast-address 10.16.7.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# Briar (A6)
subnet 10.16.1.0 netmask 255.255.255.0 {
        range 10.16.1.2 10.16.1.254;
        option routers 10.16.1.1;
        option broadcast-address 10.16.1.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# Blackbell (A7)
subnet 10.16.2.0 netmask 255.255.254.0 {
        range 10.16.2.2 10.16.3.254;
        option routers 10.16.2.1;
        option broadcast-address 10.16.3.255;
        option domain-name-servers 10.16.0.18;
        default-lease-time 600;
        max-lease-time 7200;
}
# WISE menuju Westalis (A1)
subnet 10.16.0.16 netmask 255.255.255.248 {
        option routers 192.200.0.17;
}
```

4. Menjalankan DHCP Server

```
service isc-dhcp-server start
```

### DHCP Relay

Dengan topologi yang ada, **Westalis** dan **Ostania** akan bekerja sebagai DHCP Relay

1. Pada **Westalis** dan **Ostania** sebagai DHCP Relay, dilakukan instalasi DHCP Relay

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-relay -y
```

2. Konfigurasi file `/etc/default/isc-dhcp-relay` pada **Westalis** dan **Ostania** sebagai DHCP Relay

```
SERVERS="10.16.0.19"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
```

### DNS Forwarder

Pada **Eden** sebagai DNS Server, dilakukan instalasi Bind9

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
service bind9 start
```

Kemudian konfigurasi DNS Forwarder pada file `/etc/bind/named.conf.options`

```
options {
        directory \"/var/cache/bind\";
         forwarders {
                192.168.122.1;
         };
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

Setelah itu restart bind9 dengan `service bind9 restart`

### Web Server
Node yang berperan sebagai Web Server yaitu Garden dan SSS akan menambahkan konfigurasi pada file-file sebagai berikut.

- `.bashrc`
  ```shell
  echo nameserver 192.168.122.1 > /etc/resolv.conf
  apt-get update
  apt-get install apache2 -y
  ```
 
- `/var/www/html/index.html`
  ```shell
  # Garden
  Garden
  
  # SSS
  SSS
  ```
  
- `/etc/apache2/ports.conf`
  ```shell
  # If you just change the port or add more ports here, you will likely also
  # have to change the VirtualHost statement in
  # /etc/apache2/sites-enabled/000-default.conf

  Listen 80
  Listen 443

  <IfModule ssl_module>
          Listen 443
  </IfModule>

  <IfModule mod_gnutls.c>
          Listen 443
  </IfModule>

  # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
  ```
  
### Testing

#### Forger

![image](https://user-images.githubusercontent.com/99629909/206826202-84960915-c58b-4c57-9ab4-2e1339502744.png)

#### Desmond

![image](https://user-images.githubusercontent.com/99629909/206826225-144e2eb7-e3a2-409a-86c6-e801a959789b.png)

#### Briar

![image](https://user-images.githubusercontent.com/99629909/206826241-b916fca1-c09e-4363-a2e8-e5c4935ef0d4.png)

#### Blackbell

![image](https://user-images.githubusercontent.com/99629909/206826260-3b462a6f-ba48-4dac-ad53-b289129e061c.png)


## (1) iptables pada **Strix** tanpa `MASQUERADE`

`MASQUERADE` dapat diganti dengan `SNAT` dengan tambahan `--to-source` dengan IP dari Strix sendiri yaitu `192.168.122.227`, syntaxnya adalah

```
iptables -t nat -A POSTROUTING -s 10.16.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.227
```

### Testing

#### Pada **Strix**

![image](https://user-images.githubusercontent.com/99629909/206837688-e61f04fc-67bb-4c9f-95ee-8058badfe2a4.png)

#### Pada **Westalis**

![image](https://user-images.githubusercontent.com/99629909/206837713-8e1ab04a-ec8d-4785-8d1d-d4e441c91960.png)

#### Pada **WISE**

![image](https://user-images.githubusercontent.com/99629909/206837731-8722461c-114c-4a38-9b72-8a0da4567973.png)

#### Pada **Briar**

![image](https://user-images.githubusercontent.com/99629909/206837772-cd4ef484-f047-473c-a52b-dbd13cae00ed.png)


## (2) Drop semua TCP dan UDP pada DHCP Server

Pada **Strix** dilakukan konfigurasi firewall sebagai berikut

```
iptables -A FORWARD -p tcp -d 10.16.0.19 -i eth0 -j DROP # Drop semua TCP
iptables -A FORWARD -p udp -d 10.16.0.19 -i eth0 -j DROP # Drop semua UDP
```

iptables di atas akan melalukan drop pada semua TCP dan UDP dengan tujuan **WISE** yang memiliki IP address `10.16.0.19`

### Testing

#### Ping google.com pada WISE setelah iptables

![image](https://user-images.githubusercontent.com/99629909/206753819-4af54578-e930-4953-a2d2-01679aca1b83.png)

#### Netcat **WISE** dengan **Strix** menggunakan port 18



## (3) Membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan

Limit koneksi ICMP dengan iptables pada **WISE** sebagai DHCP Server dan **Eden** sebagai DNS Server. Karena koneksinya ICMP maka akan digunakan flag `-p` dengan nilai `icmp`. Selain, itu akan digunakan limit maksimal 2 untuk akses koneksi secara bersamaan sehingga dapat menggunakan `--connlimit-above 2` dan menambahkan target `DROP` agar koneksi lainnya selain 2 koneksi tersebut ditolak.

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP
```

### Testing ping Eden (10.16.0.18) sebagai DNS Server dengan 3 client

![image](https://user-images.githubusercontent.com/99629909/206838101-61428279-d9c1-47db-9caf-e0b5046b6c49.png)

![image](https://user-images.githubusercontent.com/99629909/206838091-6d4db5a1-2c5a-4851-8238-4c7a3899e762.png)

![image](https://user-images.githubusercontent.com/99629909/206838086-f75ab1c2-740a-4af9-8bd7-50aa18a62a41.png)


## (4) Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00

Pada **Garden** dan **SSS** sebagai Web Server, dibuat firewall. Karena dibatasi waktu tertentu maka kita batasi menggunakan `-m time` dengan `--timestart` sebagai waktu mulai dapat diakses bernilai `07.00` dan `--timestop` sebagai waktu akhir dapat diakses bernilai `16.00`, serta membatasi hari berlaku dengan `--weekdays` untuk hari Senin hingga Jumat. Terdapat flag `-j` yang menentukan kapan akses akan di `ACCEPT` atau `REJECT`.

```
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```

### Testing

Ping **Garden** (10.16.0.27) pada jam kerja

![image](https://user-images.githubusercontent.com/99629909/206838298-ed459b18-66a5-4cbd-b5e1-a9d7ccf14071.png)

Ping **Garden** (10.16.0.27) pada hari libur

![image](https://user-images.githubusercontent.com/99629909/206838351-1b73a199-81d2-473a-ad54-aeb294dd7c29.png)


## (5) Setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan

Karena diminta untuk setiap request akan didistribusikan secara bergantian antara Garden dan SSS, maka akan dikonfigurasi untuk masing-masing node dengan port untuk request masing-masing node adalah 80 dan 443 dengan menggunakan `--dport`. Selain itu, akan dibatasi secara bergantian dengan menggunakan `--every 2` sehingga akan bergantian terdistribusinya dengan mengarahkan ke node lain dengan menggunakan `--to-destination`. Pada **Ostania** dilakukan konfigurasi iptables sebagai berikut:

```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.16.0.27 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.16.0.27:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.16.0.27 -j DNAT --to-destination 10.16.0.26:80
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.16.0.26 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.16.0.26:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.16.0.26 -j DNAT --to-destination 10.16.0.27:80
```

Hasil Testing
- Request ke Garden

![image](https://user-images.githubusercontent.com/99629909/206851095-fdd122b9-2936-4738-ad2c-0a6e47a215f2.png)

- Request ke SSS

![image](https://user-images.githubusercontent.com/99629909/206851128-a98177bd-ab06-435f-85c4-cc5e8a8ced7c.png)


## (6) Logging paket yang di-drop dengan standard syslog level

**Pada Wise** restart terlebih dahulu DHCP nya dengan:
```
service isc-dhcp-server restart
service isc-dhcp-server restart
```
kemudian isikan sylog syntax pada wise untuk melihat paket yang di drop .
```
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: "
iptables -A LOGGING -j DROP
```
kemudian isikan sylog pada semua node
```
service apache2 restart
service apache2 restart
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Rejected: "
iptables -A LOGGING -j REJECT
service rsyslog restart
```

Hasil Testing 

![image](https://user-images.githubusercontent.com/99629909/206854592-e88e126b-9173-4d21-9cfd-b1ddd22f01c5.png)

log dapat dicek pada path `/var/log/syslog`

![image](https://user-images.githubusercontent.com/99629909/206854549-cc53d476-2215-46a9-ab55-6ed9c9dc27e7.png)

## Kendala
1. Lupa melakukan setting resolv.conf di semua node sehingga ada node yang tidak bisa terhubung internet dan tidak bisa melakukan instalasi kebutuhan soal
2. pada testing soal nomor 5 sempat mengalami request failed karena sempat mengubah date ke waktu di luar jam kerja dan belum dikembalikan ke waktu jam kerja
