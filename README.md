# Best Practices for Docker CE/EE

- [kernel](#kernel)
- [daemon](#daemon)
- [node sizes](#node)
- [build script](#build)
- [compose example](#compose)

## OS
Use the latest. Centos 7.4 works well.

<a name="kernel"></a>
## kernel :
```bash
echo "vm.swappiness=0" >> /etc/sysctl.conf # turn off swapping unless necessary
echo "vm.overcommit_memory=1" >> /etc/sysctl.conf # allwoing oversubscription
echo "net.ipv4.neigh.default.gc_thresh1 = 80000" >> /etc/sysctl.conf # arp cache fixes
echo "net.ipv4.neigh.default.gc_thresh2 = 90000" >> /etc/sysctl.conf # arp cache fixes
echo "net.ipv4.neigh.default.gc_thresh3 = 100000" >> /etc/sysctl.conf # arp cache fixes
echo "net.ipv4.tcp_keepalive_time=600" >> /etc/sysctl.conf # decrease the tcp timeout for ipvs
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf # needed for iptables
echo "fs.may_detach_mounts=1" >> /etc/sysctl.conf # needed for host mountpoints with RHEL 7.4
echo "fs.inotify.max_user_instances=8192" >> /etc/sysctl.conf # monitor file system events
echo "fs.inotify.max_user_watches=1048576" >> /etc/sysctl.conf # monitor file system events
sysctl -p
```

<a name="daemon"></a>
## daemon :
Note that you can turn on the experimental prometheus endpoint.

```bash
docker plugin disable docker/telemetry:1.0.0.linux-x86_64-stable
echo -e "{\n \"selinux-enabled\": true, \n \"log-driver\": \"json-file\", \"log-opts\": {\"max-size\": \"10m\", \"max-file\": \"3\"}, \n \"metrics-addr\" : \"0.0.0.0:9323\", \n \"experimental\" : true \n}" > /etc/docker/daemon.json
 systemctl restart docker
```

<a name="node"></a>
## node sizing :
controllers - 4vcpu/16gb ram

workers - 4vcpu/16gb ram +

<a name="build"></a>
## build script :
<a href="https://github.com/clemenko/ucp/blob/master/do_ucp.sh">https://github.com/clemenko/ucp/blob/master/ucp.sh</a>

<a name="compose"></a>
## compose example :
<a href="https://github.com/clemenko/compose_files/blob/master/compose_all_the_things.yml">https://github.com/clemenko/compose_files/blob/master/compose_all_the_things.yml</a>
