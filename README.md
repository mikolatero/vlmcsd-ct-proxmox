# vlmcsd Container Proxmox

## Container Specification (minimum)
- Alpine 3.8
- 1 core
- 0.3GB HDD
- 64MB RAM

## INSTALL SCRIPT ##

```
apk update \
    && apk upgrade \
    && apk add --no-cache build-base gcc abuild binutils cmake git \
    && cd /root \
    && git clone https://github.com/Wind4/vlmcsd.git vlmgit \
    && cd vlmgit \
    && make \
    && chmod +x bin/vlmcsd \
    && mv bin/vlmcsd /usr/bin/vlmcsd \
    && cd /root \
    && apk del build-base gcc abuild binutils cmake git \
    && rm -rf /root/vlmgit  \
    && rm -rf /var/cache/apk/*

chmod +x /usr/bin/vlmcsd

cat <<EOF > /etc/init.d/vlmcsd
#!/sbin/openrc-run

supervisor=supervise-daemon
name=vlmcsd
command="/usr/bin/vlmcsd"
command_user=root
command_args="-D -d -t 3 -e -v"
pidfile="/run/vlmcsd.pid"

depend() {
        use logger dns
        need net
        after firewall
}
EOF

chmod +x /etc/init.d/vlmcsd

rc-update add vlmcsd
rc-service vlmcsd start
```
