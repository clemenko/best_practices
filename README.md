# Best Practices for Docker CE/EE

- [kernel](#kernel)
- [daemon](#daemon)
- [node sizes](#node)
- [build script](#build)
- [compose example](#compose)


<a name="kernel"></a>
## kernel :
```bash
echo "vm.swappiness=0" >> /etc/sysctl.conf
echo "vm.overcommit_memory=1" >> /etc/sysctl.conf
echo "net.ipv4.neigh.default.gc_thresh1 = 80000" >> /etc/sysctl.conf
echo "net.ipv4.neigh.default.gc_thresh2 = 90000" >> /etc/sysctl.conf
echo "net.ipv4.neigh.default.gc_thresh3 = 100000" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_time=600" >> /etc/sysctl.conf
echo "fs.may_detach_mounts=1" >> /etc/sysctl.conf
sysctl -p
```

<a name="daemon"></a>
## daemon :
Note that you can turn on the experimental prometheus endpoint.

```bash
docker plugin disable docker/telemetry:1.0.0.linux-x86_64-stable
echo -e "{ \n \"storage-driver\": \"overlay2\", \n \"storage-opts\": [\"overlay2.override_kernel_check=true\"], \n \"metrics-addr\" : \"0.0.0.0:9323\", \n \"experimental\" : true\n}" > /etc/docker/daemon.json
 systemctl restart docker
```

<a name="node"></a>
## node sizing :
controllers - 4vcpu/16gb ram
workers - 4vcpu/16gb ram +

<a name="build"></a>
## build script :
<a href="https://github.com/clemenko/ucp/blob/master/do_ucp.sh">https://github.com/clemenko/ucp/blob/master/do_ucp.sh</a>

<a name="compose"></a>
## compose example :
<a href="https://github.com/clemenko/compose_files/blob/master/compose_all_the_things.yml">https://github.com/clemenko/compose_files/blob/master/compose_all_the_things.yml</a>
