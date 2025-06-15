Vagrant.configure(2) do |config|
  config.vm.define "docker" do |docker|
    docker.vm.box = "bento/ubuntu-24.04"
    docker.vm.network "private_network", ip: "192.168.11.22"
    docker.vm.hostname = "docker"
    docker.vm.synced_folder ".", "/home/vagrant/sync", type: "rsync"
    docker.vm.provision :shell, :path => "docker.sh"
    docker.vm.network "forwarded_port", guest: 80, host: 80
    docker.vm.network "forwarded_port", guest: 443, host: 443
    docker.vm.network "forwarded_port", guest: 3000, host: 3000
    docker.vm.network "forwarded_port", guest: 5000, host: 50000
    docker.vm.network "forwarded_port", guest: 6379, host: 6379 
    docker.vm.network "forwarded_port", guest: 8080, host: 8080
  end
end
