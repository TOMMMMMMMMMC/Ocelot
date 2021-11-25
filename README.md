# 部署说明
1. 事先准备
```bash
apt-get update
apt-get install --no-install-recommends -y \
        build-essential \
        automake \
        cmake \
        default-libmysqlclient-dev \
        libboost-iostreams-dev \
        libboost-system-dev \
        libev-dev \
        libjemalloc-dev \
        libmysql++-dev \
        pkg-config
```
2. cd 到项目根目录

3. 构建
```bash
autoreconf -i
./configure --with-mysql-lib=/usr/lib/x86_64-linux-gnu/ \
        --with-ev-lib=/usr/lib/x86_64-linux-gnu/ \
        --with-boost-libdir=/usr/lib/x86_64-linux-gnu/
make

```
4. 更新一下数据库
```sql
ALTER TABLE `xbt_snatched` ADD `ipv6` VARCHAR(45) NOT NULL;
ALTER TABLE `xbt_files_users` ADD `ipv6` VARCHAR(45) NOT NULL;
```

5. 参考一下 ocelot.conf.dist 配置 ocelot.conf

6. 使用 nohup 启动 
```bash
nohup ./ocelot &>ocelot.nohup.log &
```

7. 配置gazelle 里的config.php
- 修改 TRACKER_HOST 为 tracker 服务器域名或者 ip
- 修改 TRACKER_PORT 为 ocelot监听的端口 或 nginx反向代理的端口 网站后台(gazelle 里的 tracker.class.php ) 不支持调用启用了 ssl 的 tracker 接口的调用，所以，借助 nginx 支持 https 后，只能配置 http 的端口


# Ocelot

Ocelot is a BitTorrent tracker written in C++ for the [Gazelle](http://whatcd.github.io/Gazelle/) project. It supports requests over TCP and can only track IPv4 peers.

## Ocelot Compile-time Dependencies

* [GCC/G++](http://gcc.gnu.org/) (4.7+ required; 4.8.1+ recommended)
* [Boost](http://www.boost.org/) (1.55.0+ required)
* [libev](http://software.schmorp.de/pkg/libev.html) (required)
* [MySQL++](http://tangentsoft.net/mysql++/) (3.2.0+ required)
* [TCMalloc](http://goog-perftools.sourceforge.net/doc/tcmalloc.html) (optional, but strongly recommended)

## Installation

The [Gazelle installation guides](https://github.com/WhatCD/Gazelle/wiki/Gazelle-installation) include instructions for installing Ocelot as a part of the Gazelle project.

### Standalone Installation

* Create the following tables according to the [Gazelle database schema](https://raw.githubusercontent.com/WhatCD/Gazelle/master/gazelle.sql):
 - `torrents`
 - `users_freeleeches`
 - `users_main`
 - `xbt_client_whitelist`
 - `xbt_files_users`
 - `xbt_snatched`

* Edit `ocelot.conf` to your liking.

* Build Ocelot:

        ./configure
        make
        make install

## Running Ocelot

### Run-time options:

* `-c <path/to/ocelot.conf>` - Path to config file. If unspecified, the current working directory is used.
* `-v` - Print queue status every time a flush is initiated.

### Signals

* `SIGHUP` - Reload config
* `SIGUSR1` - Reload torrent list, user list and client whitelist
