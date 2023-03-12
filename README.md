# haproxy-http3

## Howto buid haproxy with quic and http3 support

### 1. README

Q. Why?\
A. Haproxy does not support http3 and quic protocol by default, so we need to compile.

Q. How reliable is this for production?\
A. I do not recommend this for production web sites because Http3 and quic are quite new and most of the web servers do not support it yet. However, haproxy supports it and you can use it to proxy your traffic to a regular web server, such as Apache or Nginx.

### 3. Requirements

As we need to compile Haproxy to support quic, we need develpment packages to do this:

```bash
yum install -y rpm-build rpmdevtools pcre-devel openssl-devel zlib-devel redhat-rpm-config \
               gcc gcc-c++ make libstdc++-devel rpmlint \
               lua-devel systemd-devel systemd-devel pcre2-devel
```

We also need this repo and the files needed to build an rpm package:

`git clone https://github.com/deaniliev/haproxy-h3.git`

