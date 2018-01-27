$script = <<SCRIPT
echo Update
apt-get update

echo Install Java 8 JDK
apt-get install openjdk-8-jdk -y

echo Install Elastic Search
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
apt-get update && sudo apt-get install elasticsearch

echo Config Elastic Search
sed -i 's/#network.host: 192.168.0.1/network.host: 0.0.0.0/' /etc/elasticsearch/elasticsearch.yml
sed -i 's/#cluster.name: my-application/cluster.name: renclastic-cluster/' /etc/elasticsearch/elasticsearch.yml
sed -i 's/#node.name: node-1/node.name: renclastic-node/' /etc/elasticsearch/elasticsearch.yml

sysctl -w vm.max_map_count=262144

/bin/systemctl daemon-reload
/bin/systemctl enable elasticsearch.service
systemctl start elasticsearch.service

chmod -R 777 /var/log/elasticsearch
chmod -R 777 /var/lib/elasticsearch
chmod -R 777 /usr/share/elasticsearch
chmod -R 777 /etc/elasticsearch
chmod -R 777 /etc/default/elasticsearch

SCRIPT

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |v|
	v.memory = 2048
	v.cpus = 2
  end
  config.vm.box = "ubuntu/xenial64"
  config.vm.network 'forwarded_port', guest: 9200, host: 9200
  config.vm.network 'forwarded_port', guest: 9300, host: 9300
  config.vm.provision "shell", inline: $script
end