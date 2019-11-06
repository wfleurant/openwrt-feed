## Stuck notes
    WIP: See notes in Makefile
            include config.log:
            > checking whether iconv supports //IGNORE... no
            > checking whether iconv supports state reset... no
            > checking for iconv declaration...
            > checking pcre.h usability... yes
            > checking pcre.h presence... yes
            > checking for pcre.h... yes
            > checking for pcre_compile in -lpcre... yes
            > checking for pcre32_compile in -lpcre32... no
            > configure: error: libpcre32 is requred
            > Makefile:153: recipe for target
    '/dev/shm/openwrt-trunk/build_dir/target-mips_24kc_musl/netxms-agent-nossl/netxms-3.0.2329/.configured_68b329da9893e34099c7d8ad5cb9c940'
    failed
            > make[2]: ***
    [/dev/shm/openwrt-trunk/build_dir/target-mips_24kc_musl/netxms-agent-nossl/netxms-3.0.2329/.configured_68b329da9893e34099c7d8ad5cb9c940]
    Error 1
            > make[2]: Leaving directory
    '/dev/shm/openwrt-trunk/feeds/netxms/admin/netxms'
            > time:
    package/feeds/netxms/netxms/nossl/compile#31.53#7.06#37.07



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
ln -s /dev/shm/netxms-agent-openwrt || exit 1
echo "src-link xms $PWD/netxms-agent-openwrt" >> feeds.conf.default
./scripts/feeds update -a
./scripts/feeds install netxms
make -j$(nproc)
```

