# Flood Shield is a very fast http flood blocker

Please be aware! It's first beta realease of tool!

We sniff and parse all incoming http requests. If any IP made more than XX requests per second (with same host, method and URI) we will trigger ipset ban immediately. 

Compatibility: only Linux, Debian 7+, CentOS 6+

You could override standard hash algorithm with follofing line on code:
```C++
std::string hash_key = client_ip + ":" + host_string + ":" + method_string + ":" + path_string
```

Install FastNetMon (it will build and install all required libs):
```bash
wget https://raw.githubusercontent.com/FastVPSEestiOu/fastnetmon/master/src/fastnetmon_install.pl
perl fastnetmon_install.pl
```

Install dependency of Flood Shield:
```bash
# Debian
apt-get install -y ipset libipset-dev libipset2
```

For CentOS 6 you should build ipset from sources:
```
yum install -y libmnl-devel
cd /usr/src
wget http://ipset.netfilter.org/ipset-6.24.tar.bz2
tar -xf ipset-6.24.tar.bz2
cd ipset-6.24
./configure --prefix=/opt/ipset --with-kmod=no
make install
echo "/opt/ipset/lib" > /etc/ld.so.conf.d/ipset.conf
ldconfig
```

Build Flood Shield:
```
cd /usr/src
git clone https://github.com/FastVPSEestiOu/flood_shield.git
cd flood_shield
./build_shield.sh
```

Create ipset and iptables rules:
```bash
# Debian
ipset --create blacklist iphash --hashsize 4096
# CentOS 6:
/opt/ipset/sbin/ipset --create blacklist iphash --hashsize 4096
iptables -I INPUT -m set --match-set blacklist src -p TCP --destination-port 80 -j DROP
```

Run it:
```bash
./shield
```

By default we will ban any IP which exceed 20 requests per second with same HOST, METHOD and URI. If you want to change it, please fix in code and recompile. We sniff only 80 port by default.

Thanks:
- Kazuho Oku, Tokuhiro Matsuno, Daisuke Murase, Shigeo Mitsunari for a perfect and fast http packet [parser](https://github.com/h2o/picohttpparser)
- PF_RING team for a nice facility for [capturing packets](http://www.ntop.org/products/pf_ring/)
