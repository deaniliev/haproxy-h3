# haproxy-http3

## Howto buid haproxy with quic and http3 support

### README 1st

Q. Why?
A. Haproxy does not support http3 and quic protocol by default, so we need to compile.

Q. How reliable is this for production?
A. I do not recommend this for production web sites because Http3 and quic are quite new and most of the web servers do not support it yet. However, haproxy supports it and you can use it to proxy your traffic to a regular web server, such as Apache or Nginx.

