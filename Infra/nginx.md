# 1. Nginx

- 2004년 러시아 개발자 lgor Sysoev가 만듬
- 10K의 커넥션을 동시에 핸들링할 수 있도록 성능에 가치를 두고 만듬
    - High Performance
    - High Concurrency
    - Low Resource Usage

## 1.1. Basic Architecture

- deals with requests asynchronously

# 2. Install

## 2.1 Ubuntu

```bash
apt-get update
apt-get install nginx
```

## 2.2 CentOS

```bash
yum install epel-release
yum install nginx
service nginx start
```

## 2.3 Binary

- nginx.org에 접속해서 링크 복사

### Ubuntu

```bash
apt-get install build-essential
wget https://nginx.org/download/nginx-1.23.2.tar.gz
tar -zxvf nginx*
apt-get install libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev
./configure --sbin-path=/usr/bin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-pcre \
--pid-path=/var/run/nginx.pid \
--with-http_ssl_module
make
```

### CentOS

```bash
yum groupinstall "Development Tools"
curl https://nginx.org/download/nginx-1.23.2.tar.gz
tar -zxvf nginx*
yum install pcre pcre-devel zlib zlib-devel openssl openssl-devel
./configure --sbin-path=/usr/bin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-pcre \
--pid-path=/var/run/nginx.pid \
--with-http_ssl_module
make
```

### 2.5 서비스 등록

- [https://www.nginx.com/nginx-wiki/build/dirhtml/start/topics/examples/systemd/](https://www.nginx.com/nginx-wiki/build/dirhtml/start/topics/examples/systemd/)

```bash
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/usr/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```