# nginx 도메인(domain) 변경 방법

본문에서는 도메인 구매부터 DNS 설정방법은 다루지 않으며, 도메인구매와 DNS 설정까지 모두 마친 가정하에 설명 드립니다.

## 가. 서버셋팅
- CentOS Linux release 8.5.2111
- nginx version: nginx/1.14.1

상기 버전정보는 `cat /etc/*release*` 와 `nginx -v` 으로 확인하여 작성되었습니다.

## Nginx 서버 설정 수정 - nginx.conf 파일 수정
```shell
$ vi /etc/nginx/nginx.conf
```
```shell
# nginx.conf

http {
    ... 생략 ...

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  # 도메인 입력
        root         /usr/share/nginx/test;
        # root         /usr/share/nginx/html;

        ... 생략 ...
    }
    
    ... 생략 ...
```

`server_name` 부분에 구입한 도메인을 입력(`server_name  example.co.kr`)한 후 아래와 같 nginx 재시작

```shell
$ service nginx restart
```