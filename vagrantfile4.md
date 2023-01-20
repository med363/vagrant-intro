```bash
vagrant init
```
### provision with shell 
```bash
 config.vm.box = "ubuntu/trusty64"
  config.vm.provision :shell do |shell|
  # name of my shell if I have many script of shell and we can show the script running when we click vagrant up
    shell.name = "Provision Script 1"
 # run shell with out sudo 
    shell.privileged = "false"
 # if I put in my script $1 $2 $3 we need to define this arguments
    shell.args = ["arg1", "arg2", "arg3"]
 # place of script (external script) we can write it in gist github and after we click in Raw And copie url 
    shell.path = "provision.sh"
  end
  ```
  ## execute file
  ```bash
  vagrant up
  ```
  ### _________________________________________________
  ### Setting up a 3-node nginx load balancing architecture which will all be set up and configured via provisionnig.
  ```bash
    config.vm.define "lb1" do |lb1|
    lb1.vm.box = "ubuntu/trusty64"
    lb1.vm.network "private_network", ip: "10.0.0.10"
    lb1.vm.provision "shell", path: "lb.sh"
  end

  config.vm.define "web1" do |web1|
    web1.vm.box = "ubuntu/trusty64"
    web1.vm.network "private_network", ip: "10.0.0.11"
    web1.vm.provision :shell do |shell|
      shell.args = "1"
      shell.path = "web.sh"
    end
  end

  config.vm.define "web2" do |web2|
    web2.vm.box = "ubuntu/trusty64"
    web2.vm.network "private_network", ip: "10.0.0.12"
    web2.vm.provision :shell do |shell|
      shell.args = "2"
      shell.path = "web1"
    end
  end
  ```
  ### script of lb
  ```bash
  #!/bin/bash

echo 'Starting Provision: lb1'
sudo apt-get update
sudo apt-get install -y nginx
sudo service nginx stop
sudo rm -rf /etc/nginx/sites-enabled/default
sudo touch /etc/nginx/sites-enabled/default
echo "upstream testapp {
        server 10.0.0.11;
        server 10.0.0.12;
}
server {
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;
        root /usr/share/nginx/html;
        index index.html index.htm;
        # Make site accessible from http://localhost/
        server_name localhost;
        location / {
                proxy_pass http://testapp;
        }
}" >> /etc/nginx/sites-enabled/default
sudo service nginx start
echo "Machine: lb1" >> /usr/share/nginx/html/index.html
echo 'Provision lb1 complete'
```
### Using nginx as HTTP lb
```bash
http://nginx.org/en/docs/http/load_balancing.html
````
### script of web
```bash
#!/bin/bash

echo 'Starting Provision: web'$1
sudo apt-get update
sudo apt-get install -y nginx
echo "<h1>Machine: web"$1"</h1>" >> /usr/share/nginx/html/index.html
echo 'Provision web'$1 'complete'
```

### bellow shell provision documentation and provisionnig
```bash
https://developer.hashicorp.com/vagrant/docs/provisioning/shell
```
```bash
https://developer.hashicorp.com/vagrant/docs/provisioning
```
  
