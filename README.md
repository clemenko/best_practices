# Best Practices for Docker CE/EE

- [kernel](#kernel)
- [daemon](#daemon)
- [node sizes](#node)
- [build script](#build)
- [k8s yaml examples](#yaml)
- [compose example](#compose)

## OS
Use the latest. Centos 7.5 works well.

<a name="kernel"></a>
## kernel :
```bash
cat << EOF >> /etc/sysctl.conf

# SWAP settings
vm.swappiness=0
vm.overcommit_memory=1

# Have a larger connection range available
net.ipv4.ip_local_port_range=1024 65000

# Increase max connection
net.core.somaxconn = 10000

# Reuse closed sockets faster
net.ipv4.tcp_tw_reuse=1
net.ipv4.tcp_fin_timeout=15

# The maximum number of "backlogged sockets".  Default is 128.
net.core.somaxconn=4096
net.core.netdev_max_backlog=4096

# 16MB per socket - which sounds like a lot,
# but will virtually never consume that much.
net.core.rmem_max=16777216
net.core.wmem_max=16777216

# Various network tunables
net.ipv4.tcp_max_syn_backlog=20480
net.ipv4.tcp_max_tw_buckets=400000
net.ipv4.tcp_no_metrics_save=1
net.ipv4.tcp_rmem=4096 87380 16777216
net.ipv4.tcp_syn_retries=2
net.ipv4.tcp_synack_retries=2
net.ipv4.tcp_wmem=4096 65536 16777216

# ARP cache settings for a highly loaded docker swarm
net.ipv4.neigh.default.gc_thresh1=8096
net.ipv4.neigh.default.gc_thresh2=12288
net.ipv4.neigh.default.gc_thresh3=16384

# ip_forward and tcp keepalive for iptables
net.ipv4.tcp_keepalive_time=600
net.ipv4.ip_forward=1

# needed for host mountpoints with RHEL 7.4
fs.may_detach_mounts=1

# monitor file system events
fs.inotify.max_user_instances=8192
fs.inotify.max_user_watches=1048576
EOF
sysctl -p
```

<a name="daemon"></a>
## daemon :
Note that you can turn on the experimental prometheus endpoint.

```bash
docker plugin disable docker/telemetry:1.0.0.linux-x86_64-stable
echo -e "{\n \"selinux-enabled\": true, \n \"log-driver\": \"json-file\", \"log-opts\": {\"max-size\": \"10m\", \"max-file\": \"3\"}, \n}" > /etc/docker/daemon.json
 systemctl restart docker
```

<a name="node"></a>
## prod node sizing :
controllers - 8vcpu/32gb ram

workers - 4vcpu/16gb ram +

<a name="build"></a>
## build script :
<a href="https://github.com/clemenko/ucp/blob/master/do_ucp.sh">https://github.com/clemenko/ucp/blob/master/ucp.sh</a>

<a name="yaml"></a>
## k8s yaml examples :
<a href="https://github.com/clemenko/k8s_yaml">https://github.com/clemenko/k8s_yaml</a>

<a name="compose"></a>
## compose example :
<a href="https://github.com/clemenko/compose_files/blob/master/compose_all_the_things.yml">https://github.com/clemenko/compose_files/blob/master/compose_all_the_things.yml</a>
