# How To completely uninstall Docker:
=================================

> Step 1

```
dpkg -l | grep -i docker
```

> Step 2

To identify what installed package you have:

```
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin
```

> Step 3

> [!WARNING]
> The above commands will not remove images, containers, volumes, or user created configuration files on your host. 
> If you wish to delete all images, containers, and volumes run the following commands:

```
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /var/lib/containerd
sudo rm -r ~/.docker
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras 
```