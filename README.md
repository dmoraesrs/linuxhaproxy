# Arquivos Complementares



https://www.haproxy.org/download/1.8/doc/configuration.txt

Comandos:
#Cockpit
systemctl enable --now cockpit.socket
systemctl status cockpit.socket

#HAProxy
dnf install haproxy
cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.org
echo "" > /etc/haproxy/haproxy.cfg
vi  /etc/haproxy/haproxy.cfg
##############################
#HAProxy

#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    log         127.0.0.1 local2
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     2048
    user        haproxy
    group       haproxy
    daemon

    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats

    # utilize system-wide crypto-policies
    ssl-default-bind-ciphers PROFILE=SYSTEM
    ssl-default-server-ciphers PROFILE=SYSTEM

defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option                  http-server-close
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------
frontend main
    bind *:80
    default_backend             app

#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
backend app
    balance     roundrobin
    server  app1 ipserver:80 check
##############################


systemctl start haproxy.service
systemctl status haproxy.service

#Syslog
/etc/rsyslog.conf
echo "local2.=info /var/log/haproxy-access.log" >> /etc/rsyslog.d/haproxy.conf
echo "local2.notice /var/log/haproxy-info.log" >> /etc/rsyslog.d/haproxy.conf

systemctl restart rsyslog.service

#Firewall
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload

#SElinux
setsebool -P haproxy_connect_any 1

#APP:
MYSQL
apt-get install apache2 php php-mysql git mariadb-server mariadb-client -y
grant all on trabalho_php.* to lab01@localhost identified by '123456';
flush privileges;

https://github.com/dmoraesrs/linuxhaproxy.git
https://github.com/dmoraesrs/sistemacatalogo.git




