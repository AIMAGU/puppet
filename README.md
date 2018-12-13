# Infrastructure as Code - Puppet
Infrastructure as Code (IaC) atau Programmable Infrastructure adalah runtutan penyediaan infrastruktur IT yang dikelola melalui baris kode secara otomatis. 
Proses konfigurasi virtual mesin, cloud computing dapat di lakukan dengan mudah menggunakan IaC secara cepat dan berulang. Selain itu IaC juga berfungsi sebagai dokumentasi dalam artian setiap orang pasti tahu konfigurasi server, kebutuhan aplikasi server, dll.
Beberapa tools yang digunakan untuk infrastructure as Code diantaranya: Puppet, Terraform, Ansible, Packer, Chef & Salt Stack.
Puppet merupakan salah satu tools IaC yang bersifat opensource dimana bisa dapatkan secara Cuma-Cuma pada laman resminya https://puppet.com. Puppet ini berfungsi apabila kita memiliki 3 server dapat kita kontrol secara mudah dengan cara terpusat. Di dalam 3 server tersebut kita berikan beban yang berbeda, kita dapat menggunakan VM untuk membuat 3 server tersebut. Contohnya kita menginstal web server, konfigurasi httpd.konf, permission file dapat dilakukan secara terpusat pada satu server master. Puppet juga dapat digunakan untuk mengirim perintah langsung ke node-node server untuk menginstal aplikasi tertentu. Apabila kita ketikkan perintah di master server maka secara otomatis node-node server ke-10 tersebut juga akan terinstall.
Setelah salah satu distro linux sudah di install sekarang kita akan menginstal httpd, mysql-server.
### Master Server
Menggunakan CentOs versi lawas yaitu 6.8
```
[root@puppet manifests]# cat etc/hosts
127.0.0.1	localhost localhost.localdomain localhost4 localhost4.localdomain4
::1		localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.6	puppet.rochim.com	puppet
192.168.1.7	puppet-client.rochim.com	puppet-client
```
Tambahkan alias hostname di hosts kalian seperti pada diatas
Install Repo Puppet
``` 
[user@puppet ~]# rpm -Uvh https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
	Retrieving https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
	Preparing...                          ################################# [100%]
	Updating / installing...
	1:puppetlabs-release-pc1-0.9.2-1.el################################# [100%]
```
Install puppet server
``` 
[user@puppet ~]# sudo yum install puppet-server
```
Edit puppet.conf
```
GAMBAR
```
Edit puppet configuration seperti gambar diatas
vi /etc/puppet/puppet.conf
Tambahkan:
dns_alt_names = puppet,puppet.elsyah.net #Hostname server puppet dan domain rochim.com
Set Otomasi Startup puppet
Set otomasi startup puppet ketika server di restart dengan perintah
chkconfig puppetmaster on
```
GAMBAR
```
Buka Akses Port 8140
Membuka akses port 8140 di IPTABLES dengan perintah
IPTABLES -A INPUT -m state –state NEW -m tcp -p tcp –dport 8140 -j ACCEPT
```
GAMBAR
```
Jalankan Puppet
```
GAMBAR
```
Client Server
Lakukan instalasi yang sama pada client server seperti cara diatas.

Test hasil puppet
Pada master server Buat file baru di directory /etc/puppet/manifests
Kalian buat file baru dengan nama site.pp didalam directory /etc/puppet/manifests

vi /etc/puppet/manifests/site.pp

node 'puppet-client.elsyah.net' {
        include custom_utils
}
class custom_utils {
        package { ["httpd","nmap","telnet","vim-enhanced","traceroute"]:
                ensure => latest,
                allow_virtual => false,
        }
}
Jika kalian ingin menambahkan perintah lain, kalian bisa menambahkan dibawah fungsi package seperti contoh dibawah ini
node 'puppet-client.elsyah.net' {
        include custom_utils
}
class custom_utils {
        package { ["httpd","nmap","telnet","vim-enhanced","traceroute"]:
                ensure => latest,
                allow_virtual => false,
        }
                   -> #Simbol untuk menambahkan fungsi lain
        service { 'httpd': 
                ensure => running,
                enable => true,
                   }
}
Simpan file tersebut kemudian restart puppetmasternya dengan perintah:
/etc/init.d/puppetmaster restart.

Pada client server Buat Sertifikat Sign client server kalian ke master server dengan perintah berikut:

puppet cert sign puppet-client.rochim.com
```
GAMBAR
GAMBAR
```
Jalankan perintah puppet agent dengan perintah

puppet agent –test

Client server akan otomatis menerima perintah dari master server.
