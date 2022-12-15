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

# 3. Configuration

- Context : 설정을 포함할 수 있는 블록(scope). 중첩으로 사용 가능.
- Direct : key value 형식의 설정 값
- config 파일 자체는 Main Context

## 3.1. Virtual Host

- http context 내부에 server context에 설정
    - listen port : 포트 지정
    - server_name [ip | domain] : IP 또는 Domain 지정
    - root path : service 할 파일들의 root directory 지정
    
    ```bash
    http {
    	server {
    		listen 80;
    		server_name 43.201.135.197;
    
    		root /sites/demo;
    	}
    }
    ```
    

<aside>
💡 `nginx.config` 파일을 수정하고 나서 `nginx -t` 명령으로 파일을 확인할 수 있다.

</aside>

## 3.2. Types

- 파일의 확장자에 따라서 파일의 Content-Type 제대로 지정할 수 있게 설정해야함
- types context 내부에 `Context-Type extention` 형식으로 작성

```bash
http {
	types {
		text/html html;
		text/css css;
	}
}
```

- 근데 하나하나 다 쓰고 있을 수 없기 때문에 config 파일과 같은 경로상에 mime.types라는 파일에 이미 저장되어 있어서 포함만 해주면 됨

```bash
http {
	include mime.types;
}
```

## 3.3. Location

- 특정 요청에 대한 처리를 지정
- 요청을 매치하기 위해 여러 방법이 존재
    - Prefix match
        
        ```bash
        location /greet {
         	return 200 'Hello from NGINX "/greet" location.';
        }
        ```
        
    - Excat match
        
        ```bash
        location = /greet {
        	return 200 'Hello from NGINX "/greet" location.';
        }
        ```
        
    - REGEX match
        
        ```bash
        location ~ /greet[0-9] {
        	return 200 'Hello from NGINX "/greet" location.';
        }
        ```
        
    - case insensitive
        
        ```bash
        location ~* /greet[0-9] {
        	return 200 'Hello from NGINX "/greet" location.';
        }
        ```
        
    - Preferential
        
        ```bash
        location ^~ /Greet2 {
        	return 200 'Hello from NGINX "/greet" location.';
        }
        ```
        
- 우선순위는 자세한것 부터
    1.  Exact
    2. Preferential
    3. REGEX
    4. Prefix

## 3.4. Variables

- config 파일 내부에서 사용할 수 있는 변수
    - [관련 문서](https://nginx.org/en/docs/varindex.html)

### 3.4.1 Examples

**Host, URI, Arguments 확인**

```bash
http {
	server {
		location /inspect {
			return 200 "$host\n$uri\n$args";
		}
	}
}
```

**특정 Arguments 확인**

```bash
http {
	server {
		location /inspect {
			return 200 "$arg_name";
		}
	}
}
```

**변수 할당**

```bash
http {
	server {
		set $value 'This is variables.';

		location /inspect {
			return 200 "$value";
		}
	}
}
```

## 3.5 rewrite, return

- rewrite는 특정 uri를 target uri로 내부에서 변환
    - `rewrite source-uri target-uri`
    - 내부에서 uri가 변환되기 때문에 브라우저에서 보여지는 uri는 변경되지 않음
    - 
- return은 status code와 data를 반환
    - `return status-code data`

```bash
http {
	server {
		rewrite ^/user/\w+ /greet;

		location /greet {
			return 200 "Hello User";
		}
	}
}
```

## 3.6 Logging

- nginx에는 2가지 타입의 로그가 있음
    - Error Log
    - Access Log
- Bianry로 설치할 때 error 로그와  access로그를 /var/log/nginx 하위에 저장하도록 지정
- access_log directive로 지정
    - `access_log /var/log/nginx/log-file.log`
    - `access_log off`로 지정하면 로그 작성 중지

```groovy
http {

	include mime.types;

	server {
		listen 80;
		server_name 43.201.135.197;

		root /sites/demo;

		location /greet {
			access_log /var/log/nginx/greet.access.log
			return 200 "Hello User";
		}
	}
}
```

## 3.7 Worker Process

- 하나의 마스터 프로세스와 여러개의 worker 프로세스로 구성
- 기본적으로 하나의 마스터 프로세스와 하나의 worker 프로세스로 동작
- 실제 request를 처리하는 프로세스
- worker_processes directive로 설정
    - `worker_processes number`: worker 프로세스 개수 설정
    - CPU 코어수에 맞게 지정