
## Builds NetXMS Agent with OpenWrt SDK

Status: `Not working`  


```

#!/bin/bash

#############################################
mkdir /dev/shm/x && cd /dev/shm/x || exit 1
#############################################
git clone https://github.com/wfleurant/openwrt-feed.git \
    -b libpcre \
    netxms-agent-openwrt \
|| exit 1

#############################################
mkdir /dev/shm/o && cd /dev/shm/o || exit 1
#############################################
wget http://cdn.openwrt.org/snapshots/targets/mvebu/cortexa53/openwrt-sdk-mvebu-cortexa53_gcc-8.3.0_musl.Linux-x86_64.tar.xz
tar Jxvf ./openwrt-sdk-mvebu-cortexa53_gcc-8.3.0_musl.Linux-x86_64.tar.xz || exit 1
cd ./openwrt-sdk-mvebu-cortexa53_gcc-8.3.0_musl.Linux-x86_64 || exit 1
ln -s /dev/shm/x/netxms-agent-openwrt || exit 1
echo "src-link xms /dev/shm/openwrt-sdk-ar71xx-generic_gcc-7.4.0_musl.Linux-x86_64/netxms-agent-openwrt" >> feeds.conf.default
./scripts/feeds update -a
./scripts/feeds install netxms


```
