```bash
vagrant init
```
### cree multi nodes
```bash
config.vm.define "lb1" do |lb1|
 lb1.vm.box = "ubuntu/trusty64"
end
  
config.vm.define "web1" do |web1|
 web1.vm.box = "ubuntu/trusty64"
end
  
config.vm.define "web2" do |web2|
 web2.vm.box = "ubuntu/trusty64"
end
```
### compile vagrantfile
```bash
vagrant validate
```
### execute vagrantfile
```bash
vagrant up
```
### shutdown vm
```bash
vagrant halt
```
### supprime vm
```bash
vagrant destroy
```
