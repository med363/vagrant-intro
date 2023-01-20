```bash
vagrant init
```
### forwarding port (par default listening on ... and then we decide how we get to that port from our host machine) 
```bash
config.vm.box = "ubuntu/trusty64"
config.vm.network "forwarded_port", guest: 80, host: 8089
```
### execute
```bash
vagrant up
```
## And inside the box when install nginx 
```bash
 sudo apt-get install -y nginx
 ```
 ### then ,exit the box and type in the bash
 ```bash
 sensible-browser http://127.0.0.1:8089
 ```
 ### _______________________________________________________________________________________________###
 ### ip address dynamic
 ### _______________________________________________________________________________________________###
```bash
config.vm.box = "ubuntu/trusty64"
config.vm.network "private_network", type: "dhcp"
```
### to re'excute vagrantfile
```bash
vagrant reload
```
### in the box run 
```bash
ip a 
```
### we show address get from your DHCP
 ### _______________________________________________________________________________________________###
 ### ip address static
 ### _______________________________________________________________________________________________###
```bash
config.vm.box = "ubuntu/trusty64"
config.vm.network "private_network", ip: "ip address we want(for exemple :10.10.10.1)"
```
### to re'excute vagrantfile
```bash
vagrant reload
```
### in the box run 
```bash
ip a 
```
### we show address get
