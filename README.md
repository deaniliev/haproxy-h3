# haproxy-http3

## Howto buid haproxy with quic and http3 support

### 1. README

Q. Why do we need to compile from source?\
A. Haproxy does not support http3 and quic protocol by default, so we need to compile.

Q. What version of Haproxy will be used?\
A. I will use Haproxy 2.6 LTS. Http3 was experimentally added to haproxy 2.4, and is supported in version 2.6 LTS.

Q. What OS is required?\
A. I am explaining this for Almalinux 8, as is quite powerful and free alternative to RedHat/CentOS

Q. How reliable is this for production?\
A. I do not recommend this for production web sites because Http3 and quic are quite new and most of the web servers do not support it yet. However, haproxy supports it and you can use it to proxy your traffic to a regular web server, such as Apache or Nginx.

### 2. Requirements

As we need to compile Haproxy to support quic, we need develpment packages to do this:

```bash
yum install epel-release yum-utils
dnf config-manager --set-enabled powertools
yum install -y rpm-build rpmdevtools pcre-devel openssl-devel zlib-devel redhat-rpm-config \
               gcc gcc-c++ make libstdc++-devel rpmlint \
               lua-devel systemd-devel systemd-devel pcre2-devel
```
We also need couple of packages, provided by 3rd party repo. As this openss is not the primary subject, I wont explain how to build and update your openssl version that support quic protocol (It's called quictls). Also, this is not a simple procedure as many packages rely on openssl that is buldled by your OS vendor. 

The packages are built by [CodeIT](https://codeit.guru/):\
[openssl-quic-libs](https://repo.codeit.guru/packages/centos/8/x86_64/openssl-quic-libs-1.1.1t-1.codeit.el8.x86_64.rpm)\
[openssl-quic-devel](https://repo.codeit.guru/packages/centos/8/x86_64/openssl-quic-devel-1.1.1t-1.codeit.el8.x86_64.rpm)

The second package (`openssl-quic-devel`) will conflict with `openssl-devel`, so we need to remove it and install `openssl-quic-devel`:
```bash
rpm -e openssl-devel --nodeps
yum install https://repo.codeit.guru/packages/centos/8/x86_64/openssl-quic-libs-1.1.1t-1.codeit.el8.x86_64.rpm
yum install https://repo.codeit.guru/packages/centos/8/x86_64/openssl-quic-devel-1.1.1t-1.codeit.el8.x86_64.rpm
```
I tweaked a spec file to enable quic, which is included in this repo. We need it to compile haproxy 2.6 lts from source: 
```bash
cd ~
git clone https://github.com/deaniliev/haproxy-h3.git
```

Next, we need to prepare our environment and start the build:

```bash
cd ~
rpmdev-setuptree
cp -a haproxy-h3/haproxy-build/* rpmbuild/SOURCES
mv rpmbuild/SOURCES/haproxy.spec rpmbuild/SPECS
spectool -R -g ~/rpmbuild/SPECS/haproxy.spec
rpmlint ~/rpmbuild/SPECS/haproxy.spec
rpmbuild -ba ~/rpmbuild/SPECS/haproxy.spec
```
After the proccess is complete, you can find haproxy rpm package in `~/rpmbuild/RPMS` folder.

**Remember!** If you plan to install this package on another system, you will need to have `openssl-quic-libs` package installed!

## Post install settings

To enable http3 on haproxy you will need the following added to your frontends where applicable:

```ini
bind quic4@:443 ssl crt /path/to/certificates alpn h3
http-response set-header alt-svc "h3=\":443\"; ma=86400, h3-29=\":443\"; ma=86400"
```
