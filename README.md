# docker-vyos
VyOS inside a container for validing config files, playing with the software etc. without the heavyness of a VM.

## generating a local docker image for vyos

From original https://hub.docker.com/r/2stacks/vyos instructions :
```
sudo apt-get install -y squashfs-tools python-bs4 
curl --output vyos-latest.iso `python vyos-latest.py`
mkdir unsquashfs
mkdir rootfs
sudo mount -o loop vyos-latest.iso rootfs
sudo unsquashfs -f -d unsquashfs/ rootfs/live/filesystem.squashfs
sudo tar -C unsquashfs -c . | docker import - vyos
sudo umount rootfs
```

## starting a VyOS docker instance

I am providing a docker-compose.yml and some initial config file (config.init), opiniated with DNS and SSH. The config directory will receive the configuration of VyOS as stored in the system.

How does this works ? This docker-compose will use the init as entrypoint of the squashfs image from VyOS to initialize most of VyOS components as the normal iso. The trick is to change vyos-config init script by a special one that loads the config.init mounted by docker.

```
docker-compose up -d
```

## accessing the VyOS prompt

Just running the following command after a couple of seconds to let the initial configuration settle in:
```
docker-compose exec -u vyos vyos /bin/vbash
```

Then one can interact with the VyOS prompt as usual
```
show configuration
configure
set system time-zone Europe/Madrid
commit
save
exit
```